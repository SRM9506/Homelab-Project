# 2026-06-11 — Incident: Read-Only Filesystem Recovery & Cloudflared Bridge Networking Fix

## Objective
Recover from a read-only root filesystem on Ubuntu Server that took down
n8n and Docker writes, and fix the Cloudflare Tunnel routing to n8n that
broke after recovery.

## What Happened

### Symptom 1 — n8n SQLITE_READONLY Error
- Attempted to execute the daily log reminder workflow in n8n
- Got error: `SQLITE_READONLY: attempt to write a readonly database`
- The scheduled 10:30pm Telegram reminder did not fire

### Symptom 2 — Docker Couldn't Restart Containers
- Attempting to restart the n8n container failed with:
  `Cannot restart container: open /var/lib/docker/.../hosts: read-only file system`
- This revealed the problem was bigger than n8n — the host filesystem itself
  had gone read-only

### Root Cause — Read-Only Root Filesystem
- `sudo dmesg` showed systemd-journald repeatedly failing to write:
  `Failed to write entry ... Read-only file system`
- Linux had remounted the root filesystem read-only after detecting
  filesystem errors — a protective measure to prevent further corruption
- Every service that needed to write (Docker, n8n's SQLite DB, journald)
  was failing as a result

## Resolution

### Filesystem Recovery
- Performed a clean **Shutdown** of the Ubuntu Server VM from Proxmox
  (shutdown, not reset, to avoid further corruption)
- On reboot, Linux automatically ran fsck against the dirty filesystem
- Errors were minor — fsck repaired them and the system booted normally
- Confirmed recovery: root filesystem mounted read-write again, 55GB free,
  all 16 Docker containers came back up healthy

### Cloudflared Tunnel Routing Fix
- After recovery, n8n was confirmed listening on port 5678
  (`ss -tlnp | grep 5678` showed it bound on 0.0.0.0:5678)
- But the n8n public hostname returned **502 Bad Gateway**
- cloudflared logs showed: `Unable to reach the origin service ...
  connection reset by peer` when reaching the LAN IP origin
- Diagnosis: cloudflared runs as a **bridge-network Docker container**.
  - `localhost:5678` from inside the container points at the container
    itself — nothing there.
  - The LAN IP origin was getting connection-reset.
- Fix: changed the n8n public hostname origin in the Cloudflare dashboard
  to the **Docker bridge gateway address**: `http://172.17.0.1:5678`
  - 172.17.0.1 is how a bridged container reaches ports published on the
    host. This is the reliable path.
- Verified from host with:
  `curl -s -o /dev/null -w "%{http_code}" http://172.17.0.1:5678`
- n8n loaded correctly after the change. SQLITE_READONLY error also gone
  (it was a downstream symptom of the read-only filesystem, not a real
  database permission problem).

## What I Learned
- Linux remounts a filesystem read-only to protect itself when it detects
  errors — the fix is a clean reboot so fsck can run against the unmounted
  filesystem
- A read-only filesystem cascades into everything: Docker can't write
  container metadata, SQLite-backed apps throw read-only errors, journald
  can't log. The app-level errors are symptoms, not the cause.
- A bridge-network Docker container (cloudflared) **cannot** reach host
  services via `localhost` — localhost is the container's own loopback
- The Docker bridge gateway IP (172.17.0.1) is the correct origin address
  for a bridged cloudflared container to reach host-published ports
- Always diagnose the layer below before chasing the app-level error
- A single read-only event is more likely a dirty filesystem from an
  unclean shutdown than a dying drive — confirm with SMART before assuming
  hardware failure. Key SMART fields for an SSD: Media/Data Integrity
  Errors (should be 0), Available Spare (should be ~100%), Percentage Used
  (wear), and Critical Warning (should be 0x00).

## Disk Health Check — SMART Results (Resolved)
Checked SMART data on the Proxmox host for the NVMe SSD (where the VM root
disks live) to rule out hardware failure as the cause:
- **SMART overall health: PASSED**
- **Critical Warning: 0x00** — none
- **Media and Data Integrity Errors: 0** — drive has never corrupted data
- **Available Spare: 100%** (threshold 10%) — no spare blocks consumed
- **Percentage Used: 5%** — ~95% of write endurance remaining
- **Power On Hours: ~12,684** (used drive, modest runtime)
- **Unsafe Shutdowns: 77** — incremented by the hard resets/reboots during
  recent troubleshooting sessions

**Conclusion:** The SSD is healthy — not a hardware failure. The read-only
filesystem was a *logical* error caused by an **unclean shutdown** leaving
the filesystem dirty; fsck repaired minor errors on reboot. Root cause was
abrupt power-off during troubleshooting, not a failing drive.

**Prevention:** Always use Proxmox "Shutdown" (clean ACPI shutdown), never
"Stop" (hard power-off), for VMs. Shut the host down gracefully. This is
what prevents the dirty-filesystem → read-only cascade from recurring.

## Watch / Follow-Up
- Drive is healthy, but keep an eye out — if read-only remounts recur
  *despite* clean shutdowns, re-check SMART (especially Available Spare and
  Media Errors trending).
- The n8n tunnel origin now depends on 172.17.0.1:5678. If the cloudflared
  container is ever rebuilt, this address must be preserved.

## Next Steps
- Continue with the email triage pipeline (Gmail OAuth → Ollama → Telegram)
- Consider documenting the *arr media stack (Sonarr, Radarr, Prowlarr,
  Jellyseerr, qBittorrent, Flaresolverr) — added since last logged
- Monitor for any recurrence of the read-only filesystem issue
