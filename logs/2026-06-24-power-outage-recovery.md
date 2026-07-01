# 2026-06-24 — Incident: Power Outage Recovery + Media Disk Expansion

## Objective
Recover the homelab after a power outage occurred while away, then expand the
Ubuntu Server media disk that was running low.

## The Incident
- Returned from vacation to find Immich's Postgres container reporting
  unhealthy in Portainer (17 running, 3 stopped, 1 unhealthy).
- A restart of the Postgres container failed with: `read-only file system`.
- The Proxmox console for Ubuntu Server showed the root filesystem had gone
  **read-only**, with journald errors: "Failed to write entry ... Read-only
  file system."

## Diagnosis
- Root cause: a **power outage occurred while away**, hard-killing the
  Proxmox host and all VMs with no graceful shutdown. The unclean shutdown
  caused Ubuntu Server's root ext4 filesystem to remount read-only as a
  protection mechanism.
- Disk hardware was already ruled out as the cause in a prior session (SMART
  was healthy) — so this was confirmed power/shutdown-related, not failing
  hardware.
- The read-only filesystem is why Postgres (and other services) couldn't
  write and went unhealthy.

## Resolution

### 1. Filesystem recovery
- Rebooted Ubuntu Server cleanly. On boot, systemd-fsck ran automatically on
  the root device and `/dev/sdb`, repairing and remounting read-write.
- Verified: `mount | grep ' / '` showed `ext4 (rw,relatime)` — back to
  read-write.

### 2. Service recovery (cascading)
- After reboot, several containers showed unhealthy from startup churn:
  immich-server, immich-machine-learning, homepage. These settled / were
  cleared with a clean stack restart (`docker compose restart`).
- Immich Postgres returned healthy once the filesystem was writable.
- **Nextcloud login broke** ("wrong password") — the outage scrambled the
  auth/session state. Fixed by resetting the password via occ:
  ```
  docker exec -u www-data -e OC_PASS="<newpass>" nextcloud \
   php occ user:resetpassword <admin-user> --password-from-env
  ```
  (The interactive prompt wasn't capturing input, so the env-variable method
  was used instead.)
- Confirmed all containers healthy: `docker ps --filter health=unhealthy`
  returned empty.

### 3. Media disk expansion
- `/mnt/media` had climbed to 84% (235GB/295GB) from photo-library growth.
- Expanded it using the safe expand-in-place method:
  - Proxmox: VM 100 -> Hardware -> resized the media disk +100GB increment
    (300GB -> 400GB). Done live, no shutdown needed.
  - In the VM: confirmed with `lsblk` that `/mnt/media` is mounted on the
    whole disk `/dev/sdb` (no partition table), so no growpart was needed.
  - Grew the filesystem directly: `sudo resize2fs /dev/sdb`
  - Result: `/mnt/media` now 393GB, 138GB free, down to 64% used.
- ~457GB still unallocated in the DataHDD pool for future growth.

## What I Learned
- An unclean shutdown (power loss) can remount the root filesystem read-only
  as protection; the fix is a clean reboot so fsck repairs and remounts rw
- A read-only filesystem cascades: containers can't write, Postgres goes
  unhealthy, Docker can't even restart containers ("read-only file system")
- After a hard outage, services need a clean restart to fully recover, and
  some (Nextcloud) may need credential/session repair
- `occ user:resetpassword --password-from-env` is the reliable Nextcloud
  password reset when the interactive prompt won't capture input
- Expand-in-place is the safe way to grow storage: resize the disk in
  Proxmox, then resize2fs in the guest. When the filesystem is on the whole
  disk (no partition), skip growpart and resize2fs the device directly.
- Disk growth is one-way (easy to grow, hard to shrink) — grow when needed,
  not preemptively

## Prevention / Follow-Up
- **Get a UPS.** This is the real fix. A power outage while away hard-killed
  everything and caused the read-only corruption + service cascade. A UPS
  rides through short outages and triggers a graceful host shutdown on long
  ones, preventing this from recurring. This has now happened more than once
  from power events — it's the clear preventive action.
- Monitor `/mnt/media` usage as the library grows; expand again from the
  ~457GB free pool when needed.
- Still pending: secure the 2TB drive with ~15 years of photos (only copy) —
  copy to a second location before importing to Immich.

## Next Steps
- Acquire and configure a UPS with graceful-shutdown signaling to Proxmox
- Continue toward a proper 3-2-1 backup (local second copy + Backblaze B2
  off-site) for the Immich library
