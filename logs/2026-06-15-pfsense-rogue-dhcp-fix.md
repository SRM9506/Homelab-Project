# 2026-06-15 — Incident: Rogue DHCP Server (pfSense) on Main Network

## Objective
Diagnose and fix why a household member's work laptop lost internet when the
Proxmox host was rebooted — and why a new VM kept landing on the wrong subnet.

## What Happened
- After rebooting Proxmox, a household member's **work laptop** lost WiFi,
  while none of my own devices were affected.
- Separately, a newly built VM kept pulling a x.x.2.x address instead of
  the main x.x.1.x network.

## Diagnosis
- Checked the work laptop's config: it had an IP of x.x.2.x with a
  gateway of x.x.2.1 — i.e. it was routing through pfSense, not the
  main router.
- Root cause: **two DHCP servers active on the same network** — the main
  router (x.x.1.x) and the pfSense VM (x.x.2.x). Whichever answers
  a device's DHCP request first wins, so devices landed on either subnet
  semi-randomly.
- The pfSense DHCP server was **leftover config from an abandoned WireGuard
  setup** (the project was dropped in favor of Cloudflare Tunnel, but the
  pfSense LAN interface + DHCP were never torn down). pfSense's LAN was
  bridged onto the same bridge as the main network, so it was broadcasting
  DHCP to the whole household.
- pfSense interfaces confirmed: WAN (em0), LAN (em1, DHCP enabled), and
  OPT1 = tun_wg0 (the leftover WireGuard tunnel interface — the smoking gun).

## Resolution
- In pfSense: **Services -> DHCP Server -> LAN -> unchecked "Enable DHCP
  server on LAN interface" -> Save.**
- This stops pfSense from handing out addresses; the main router becomes the
  only DHCP server again.
- Released/renewed the work laptop's lease (ipconfig /release + /renew) — it
  returned to a x.x.1.x address with the correct main-router gateway.
- Verified via ipconfig that the laptop's gateway is back to the main router.

## What I Learned
- A "rogue DHCP server" — two DHCP servers on one network — causes devices
  to land on different subnets unpredictably, breaking connectivity
- A homelab firewall VM should NEVER be in the path of household/production
  internet; lab infrastructure gets rebooted and tinkered with constantly
- Abandoned project config (here, a dropped WireGuard setup) can linger and
  cause real problems long after the project is shelved
- The fix is to ensure only ONE DHCP server runs on the main network, OR to
  isolate the lab firewall onto its own bridge so it can't leak DHCP

## Follow-Up / To-Do
- Revisit pfSense properly: decide whether to keep it off / isolated for
  future VLAN-segmentation lab practice, and disable its auto-start so it
  can't re-introduce the issue on reboot.
- If pfSense DHCP is ever re-enabled for lab work, it must be on an isolated
  bridge, not the main network.

## Next Steps
- Disable pfSense auto-start
- Plan an isolated bridge for pfSense before any future network-lab work
