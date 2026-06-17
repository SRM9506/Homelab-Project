# 2026-06-15 — Lubuntu Daily-Driver VM (RDP) + Desktop Comparison

## Objective
Build a lightweight Linux VM to use as an RDP daily driver from the MacBook
for email and general work, and evaluate whether a different desktop would
serve better.

## What Was Accomplished

### Storage Planning
- Reviewed Proxmox storage before building: local-lvm (NVMe) had ~223GB
  free, DataHDD ~557GB free. Confirmed plenty of room for new VMs.
- Learned the disk-growth path: VM disks can be expanded later from free
  physical space (resize disk in Proxmox -> growpart + resize2fs in guest).
  Correct approach is always expand-in-place, never migrate storage.

### Lubuntu VM Build (VM 106)
- Created VM: 3 cores, 6GB RAM, 40GB disk on local-lvm (NVMe),
  q35 + OVMF (UEFI)
- Installed Lubuntu 26.04 LTS (LXQt desktop)
- Set to start at boot (daily driver should always be available)

### Made It Reachable
- Installed openssh-server, xrdp, qemu-guest-agent (Lubuntu ships with none)

## Challenges & Resolutions

### 1. VM Landed on Wrong Subnet (x.x.2.x)
**Problem:** The new VM pulled a x.x.2.x DHCP lease and the MacBook
(on x.x.1.x) couldn't reach it — different subnets, no routing.
**Diagnosis:** A second DHCP server (pfSense, leftover from an abandoned
WireGuard setup) was answering DHCP on the same bridge, competing with the
main router. The VM happened to get pfSense's lease.
**Resolution (short-term):** Set a static IP on x.x.1.x via the GUI
network manager. The connection was named `netplan-ens18` (not `ens18`),
which is why the first nmcli up/down attempt errored. (Root cause later
fixed properly — see the pfSense DHCP log.)

### 2. xrdp Connected but Desktop Crashed
**Problem:** RDP connected but immediately dropped. xrdp logs showed
`Xorg server closed connection`.
**Diagnosis:** On modern Ubuntu derivatives, xrdp's `startlxqt` crashes
because it tries to use the system's global D-Bus session, which fails over
remote connections.
**Resolution:** Wrap the session in a fresh, isolated D-Bus instance:
```
rm -f ~/.xsession ~/.xsessionrc
echo "dbus-run-session startlxqt" > ~/.xsession
chmod +x ~/.xsession
sudo systemctl restart xrdp
```
The `dbus-run-session` wrapper gives the remote session its own isolated
bus instead of fighting the global one. RDP desktop then worked cleanly.

## Desktop Comparison (Lubuntu vs Fedora KDE)
- Built a second VM (Fedora KDE, VM 107) to test whether a richer desktop
  would beat Lubuntu for daily RDP use.
- Fedora KDE uses Plasma 6's **native RDP server** (better protocol than
  xrdp) — had to disable xrdp first since it was occupying port 3389.
- Finding: app/browser performance was fast on KDE, but **menu navigation
  and window movement felt clunky over RDP** — KDE's animations/effects
  don't stream as smoothly as LXQt's minimal desktop.
- Conclusion: **Lubuntu (LXQt) wins for RDP** — lightweight desktops stream
  far more smoothly than feature-rich ones. Deleted the Fedora KDE VM.

## What I Learned
- Lightweight desktops (LXQt, Xfce) RDP much more smoothly than heavy ones
  (GNOME, KDE) because there's less visual animation to stream
- The xrdp + D-Bus session isolation fix is the key to getting modern
  Ubuntu/LXQt desktops working over RDP
- KDE Plasma 6 has a built-in native RDP server (no xrdp needed), which is
  technically superior — but the desktop weight still hurts RDP feel
- VM disks can be grown later from spare physical space; expand-in-place is
  the safe path, never migrate
- NetworkManager connection names matter (netplan-ens18 vs ens18) when
  using nmcli

## Next Steps
- Possibly test Linux Mint Xfce as a middle ground (more polished than
  Lubuntu, still light enough to RDP well) — optional
- Keep lab VMs (Kali/Win Server/Metasploitable/Wazuh) off until needed
