# Day 4 — Remote Access, Portfolio & Professional Setup
Date: May 26, 2026

## Objective
Set up secure remote access to the homelab for use while traveling,
deploy a professional portfolio website, and establish a complete
online professional presence.

## What Was Accomplished

### Cloudflare Tunnel — Remote Access
- Deployed cloudflared Docker container on Ubuntu Server
- Created Cloudflare Zero Trust tunnel connecting homelab to Cloudflare
- Configured public hostname routes for all services
- All services now accessible from anywhere with real HTTPS certificates:
  - https://home.mondol.dev
  - https://jellyfin.mondol.dev
  - https://nextcloud.mondol.dev
  - https://portainer.mondol.dev
  - https://proxmox.mondol.dev
  - https://pfsense.mondol.dev
- No port forwarding required — outbound tunnel bypasses ISP restrictions
- Verified working on cellular (5G) with WiFi disabled

### WireGuard VPN Attempt
- Installed WireGuard package on pfSense
- Configured WireGuard tunnel with iPhone as peer
- Discovered Verizon Fios blocks all inbound UDP traffic at ISP level
- Tested multiple ports — all blocked
- Tested DMZ host and Low Security firewall settings — still blocked
- Switched to Cloudflare Tunnel as superior alternative

### Network Troubleshooting
- Ubuntu Server lost static IP after router changes
- Fixed by configuring static IP in /etc/netplan/00-installer-config.yaml
- Applied netplan configuration to permanently set static IP
- Disabled pfSense LAN DHCP interference with main network

### Portfolio Website — mondol.dev
- Purchased mondol.dev domain through Cloudflare ($10/year)
- Built professional portfolio in HTML/CSS with dark terminal aesthetic
- Deployed to Cloudflare Pages with automatic GitHub deployment
- Portfolio includes: hero, about, skills, projects, experience,
  certifications roadmap, and contact sections
- Live at https://mondol.dev

### LinkedIn Professional Refresh
- Updated headline to reflect IT/cybersecurity career transition
- Cleaned up skills to focused relevant set
- Added new IT skills: Proxmox, Docker, Linux, pfSense, DNS,
  Virtualization, Cybersecurity, Technical Documentation
- Added mondol.dev to contact info
- Added homelab project link

### Master Career Roadmap
- Created interactive HTML roadmap with 6 phases
- Pairs certification study with homelab projects
- Clickable checkboxes with progress tracking
- Covers: A+, Network+, Security+, AWS, eJPT, BTL1, OSCP path

## Services Running
| Service | Local | Public |
|---|---|---|
| Homepage | http://home.lan | https://home.mondol.dev |
| Proxmox | https://proxmox.lan | https://proxmox.mondol.dev |
| pfSense | https://pfsense.lan | https://pfsense.mondol.dev |
| Jellyfin | http://jellyfin.lan | https://jellyfin.mondol.dev |
| Nextcloud | http://nextcloud.lan | https://nextcloud.mondol.dev |
| Portainer | http://portainer.lan | https://portainer.mondol.dev |
| Uptime Kuma | http://uptime.lan | - |
| Nginx Proxy Manager | http://npm.lan | - |
| Pi-hole | http://pihole.lan | - |
| Kali Linux | kali.lan (RDP) | - |
| Minecraft Bedrock | Local network only | - |

## Challenges & Resolutions

### 1. Verizon Fios Blocking All Inbound Ports
**Problem:** WireGuard VPN unreachable from internet despite correct
port forwarding rules on router.
**Diagnosis:** Verizon Fios blocks all inbound UDP traffic at ISP
level regardless of router configuration.
**Resolution:** Switched to Cloudflare Tunnel which works outbound —
no inbound ports required. More secure and easier to manage.

### 2. Ubuntu Server Lost Static IP
**Problem:** After reboots and network changes Ubuntu Server received
IP from pfSense LAN DHCP instead of its static address.
**Diagnosis:** pfSense LAN DHCP server was active on the same bridge
as the main network, competing with router DHCP.
**Resolution:** Configured permanent static IP via netplan with
dhcp4: no.

### 3. Homepage Host Validation Failed
**Problem:** Accessing home.mondol.dev returned host validation error.
**Resolution:** Recreated Homepage container with
HOMEPAGE_ALLOWED_HOSTS environment variable including mondol.dev domain.

### 4. Portainer 502 Bad Gateway
**Problem:** Cloudflare tunnel to Portainer returned 502 error.
**Resolution:** Changed tunnel route type from HTTPS to HTTP.

## What I Learned
- How Cloudflare Tunnel works and why it's superior to port forwarding
- How ISPs can block traffic regardless of router configuration
- How to configure static IPs permanently using netplan on Ubuntu
- How to deploy a static website to Cloudflare Pages
- How DNS-01 certificate challenges work for local services
- The difference between WireGuard VPN and Cloudflare Tunnel approaches

## Next Steps
- Fix Pi-hole UDP DNS integration
- Deploy Windows Server 2022 with Active Directory
- Deploy Metasploitable and DVWA vulnerable targets
- Implement Wazuh SIEM
- Set up n8n workflow automation
- Start CompTIA A+ studying (Professor Messer)
