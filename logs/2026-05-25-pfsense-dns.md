# 2026-05-25 — pfSense, DNS, Services & Security Lab

## Objective
Deploy pfSense firewall, configure local DNS for clean service access,
expand the homelab with monitoring and security tools, and set up
Kali Linux as a dedicated security lab with remote desktop access.

## What Was Accomplished

### pfSense Firewall
- Deployed pfSense CE 2.7.2 VM (2 cores, 1GB RAM, 20GB disk on SSD)
- Configured WAN and LAN interfaces
- Added permanent WAN firewall rule to allow HTTPS GUI access
- Configured DNS Resolver with host overrides for all services
- Added Access List to allow local network to query DNS
- Disabled HTTP_REFERER enforcement for proxy access
- Set router DNS to pfSense so all devices use it automatically

### Local DNS & Nginx Proxy Manager
- Configured host overrides for all services using .lan domain
- Changed domain from .local to .lan to fix macOS DNS conflict
- Set up Nginx Proxy Manager proxy hosts for all services
- All services now accessible by name with no port numbers required:
  - jellyfin.lan, nextcloud.lan, portainer.lan
  - uptime.lan, npm.lan, pfsense.lan, home.lan

### Pi-hole
- Deployed Pi-hole Docker container on Ubuntu Server
- DNS integration with pfSense partially working
- UDP port 53 issue to be resolved on Day 4

### Kali Linux VM
- Created Kali Linux 2026.1 VM (4 cores, 4GB RAM, 80GB on SSD)
- Assigned static IP on local network
- Installed and configured xrdp for remote desktop access
- Fixed xrdp window manager config in /etc/xrdp/startwm.sh
- Full XFCE desktop accessible via RDP from MacBook and Nobara PC
- SSH access enabled and working
- Added kali.lan DNS entry in pfSense

### Homepage Dashboard
- Deployed Homepage dashboard Docker container
- Configured with all homelab services organized by category:
  - Infrastructure: Proxmox, pfSense
  - Media: Jellyfin, Nextcloud
  - Management: Portainer, NPM, Uptime Kuma, Pi-hole
  - Security: Kali Linux
  - Gaming: Minecraft Bedrock

### Minecraft Bedrock Server
- Deployed itzg/minecraft-bedrock-server Docker container
- Accessible from iPhone, Xbox, and PC Bedrock Edition
- Server name: Shark's Homelab Server
- World data stored on Ubuntu Server

### System Maintenance
- Configured MSI laptop lid to stay on when closed
  (HandleLidSwitch=ignore in /etc/systemd/logind.conf)
- Installed htop on Proxmox and Ubuntu Server
- Confirmed BIOS boot order (Proxmox as primary)

## Services Running
| Service | Purpose |
|---|---|
| Proxmox | Hypervisor |
| pfSense | Firewall |
| Homepage | Dashboard |
| Jellyfin | Media Server |
| Nextcloud | Cloud Storage |
| Portainer | Docker Management |
| Uptime Kuma | Monitoring |
| Nginx Proxy Manager | Reverse Proxy |
| Pi-hole | Ad Blocker |
| Kali Linux | Security Lab |
| Minecraft Bedrock + Java | Game Server |
| Windows Server 2022 | Active Directory Domain Controller |
| Metasploitable2 | Vulnerable Target (Lab) |
| Wazuh SIEM | Security Monitoring |

## Challenges & Resolutions

### 1. pfSense Serial Console Issue
**Problem:** First pfSense installer ISO (Netgate v1.2) failed to
display via VGA console — showed getty errors in a loop.
**Diagnosis:** Netgate installer ISO uses serial console only,
not compatible with standard VGA display in Proxmox.
**Resolution:** Downloaded official pfSense CE 2.7.2 ISO directly
to Proxmox — works with standard noVNC console out of the box.

### 2. DNS Not Resolving .lan Domains
**Problem:** .local domains not resolving on macOS due to Bonjour
conflict. .lan domains not resolving due to pfSense Access List
blocking external queries.
**Resolution:** Switched all host overrides from .local to .lan.
Added Access List in DNS Resolver allowing local network.
Set router DNS to pfSense so all devices use it automatically.

### 3. xrdp Disconnecting Immediately
**Problem:** RDP connection to Kali established then immediately
disconnected — window manager exiting in 0 seconds.
**Diagnosis:** xrdp startwm.sh script not finding XFCE session.
**Resolution:** Added XFCE4 startup commands to /etc/xrdp/startwm.sh
before the default Xsession fallback lines.

### 4. Kali on Wrong Network Subnet
**Problem:** Kali received IP from pfSense LAN DHCP instead of
main network range.
**Resolution:** Set static IP directly in /etc/network/interfaces.

### 5. Homepage Host Validation Error
**Problem:** Homepage blocked access with host validation error.
**Resolution:** Set HOMEPAGE_ALLOWED_HOSTS environment variable
to include both IP and domain name.

## What I Learned
- How pfSense firewall rules and DNS resolver work
- Why .local conflicts with macOS mDNS/Bonjour
- How xrdp integrates with desktop environments on Linux
- How Nginx Proxy Manager routes traffic by domain name
- How local DNS host overrides eliminate need for IP memorization
- How to diagnose and fix RDP session startup failures

## Next Steps
- Fix Pi-hole UDP DNS integration with pfSense
- Deploy Windows Server 2022 with Active Directory
- Deploy Metasploitable vulnerable target VM
- Implement Wazuh SIEM for centralized monitoring
- Set up WireGuard VPN on pfSense for remote access
- Set up retro gaming server
