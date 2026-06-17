# 2026-06-16 — Proxmox Host Maintenance: Repos, Kernel Update & Tailscale Access

## Objective
Fix the Proxmox host's update repositories so it can patch, apply the
pending updates, and set up a robust remote-management path independent of
any VM.

## What Was Accomplished

### Repository Fix
- The host showed a "Some suites are misconfigured" warning.
- Root cause: a stale repository line in /etc/apt/sources.list was pointing
  at the old Debian suite (`bookworm`) while the rest of the system is on
  `trixie` (Proxmox 9 / Debian 13).
- Fix: disabled the stale bookworm no-subscription line in the GUI
  (Repositories panel). The correct trixie no-subscription repo already
  existed in proxmox.sources, so nothing was lost.
- Enterprise repos were already disabled; no-subscription repo confirmed
  active. The remaining "non-production repository" notice is expected and
  correct for a homelab — ignored.

### System Update
- Ran `apt update && apt upgrade -y`.
- 34 packages upgraded + 1 new kernel installed (proxmox-kernel-7.0.6-2-pve).
- pve-manager updated 9.2.2 -> 9.2.3.
- Confirmed which VMs auto-start before rebooting: only VM 100 (Ubuntu
  Server) and VM 101 (pfSense) had onboot=1.
- Rebooted the host cleanly. Confirmed new kernel loaded (7.0.6-2-pve) and
  resource usage healthy afterward.

### Tailscale Remote Access to Proxmox
- Confirmed Tailscale running on the Proxmox host (it updated to 1.98.4
  during the upgrade).
- Established that reaching Proxmox at https://<tailscale-ip>:8006 is the
  most robust remote path — it connects to the host directly and does NOT
  depend on any VM (unlike the Cloudflare Tunnel path, which runs as a
  container on Ubuntu Server).
- Saved/tested the Tailscale connection on MacBook and iPhone; verified it
  works off-WiFi on cellular.
- Confirmed tailscaled is enabled to start on boot.

## What I Learned
- Proxmox repo "misconfigured suite" warnings come from stale suite names
  left over from a major-version upgrade (bookworm -> trixie); fix is to
  disable the old line, not add a new repo
- Only VMs with onboot=1 come back after a host reboot; everything else
  must be started manually (intentional — keeps lab VMs off until needed)
- Tailscale-into-Proxmox is the ideal remote-management path because it
  reaches the host directly and survives even if every VM is off — unlike
  the tunnel path which depends on Ubuntu Server being up
- Always reboot the host only when physically reachable or confident it'll
  come back; individual VM start/stop is safe to do remotely, host reboots
  are riskier

## Resource Management Pattern (learned today)
- Steady-state: keep only Ubuntu Server + pfSense (being retired) + Lubuntu
  running. Idle RAM dropped from ~60% to ~20%.
- Spin up the security lab (Kali / Windows Server / Metasploitable / Wazuh)
  as a group only for AD attack/defense sessions, then shut them off.

## Next Steps
- Verify Tailscale auto-reconnect survives a reboot (test while home)
- Disable pfSense auto-start (ties into the rogue-DHCP fix)
