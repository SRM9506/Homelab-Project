# 2026-05-28 — Remote Access, SSH & RDP
## Objective
Set up secure remote access to homelab machines from anywhere using
Cloudflare browser-based SSH and Tailscale for RDP.

## What Was Accomplished

### Cloudflare Browser-Based SSH
- Added tunnel routes for Ubuntu Server and Kali Linux using ssh:// protocol
- Created individual Access applications for each with SSH protocol enabled
- Both machines accessible from any browser anywhere in the world
- Protected behind Cloudflare Access one-time PIN authentication
- No client or app required — works on MacBook and iPhone browser

### Tailscale VPN — Remote RDP Access
- Installed Tailscale on Kali Linux, MacBook, and iPhone
- All devices authenticated under same Tailscale account
- Verified connectivity via ping between devices
- Configured Microsoft Remote Desktop on MacBook using Tailscale IP
- Full Kali XFCE desktop accessible from MacBook and iPhone remotely
- Works through any NAT or ISP restriction — no port forwarding needed

### Windows Server 2022 — Initial Setup
- Deployed Windows Server 2022 VM (4 cores, 8GB RAM, 60GB SSD on local-lvm)
- Installed VirtIO guest drivers for disk and network support
- Configured static IP on local network
- Installed and enabled OpenSSH server
- Set PowerShell as default SSH shell
- Installed Tailscale on Windows Server for remote RDP access
- RDP working via Microsoft Remote Desktop using Tailscale IP
- Promoted server to Active Directory Domain Controller
- Domain: homelab.local, NetBIOS: HOMELAB
- Domain functional level: Windows Server 2016

## What I Learned
- How Cloudflare browser-based SSH works through Zero Trust
- How Tailscale creates a private mesh network between devices
- Combining Cloudflare Access (SSH) and Tailscale (RDP) for complete
  remote access coverage
- How to promote a Windows Server to a Domain Controller
- How to enable and configure OpenSSH on Windows Server

## Next Steps
- Create OUs and users in Active Directory
- Join Kali Linux to the domain
- Deploy Metasploitable and DVWA
- Implement Wazuh SIEM
- Fix Pi-hole UDP DNS integration
- Start CompTIA A+ studying
