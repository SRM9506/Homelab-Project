# Day 1 — Initial Infrastructure Setup
Date: May 23, 2026

## Objective
Establish the Proxmox VE hypervisor on the MSI workstation to serve 
as the foundation for my virtualization, networking, and security labs.

## Hardware Configuration
- **Host:** MSI GF75 Thin (10UEK-048)
- **CPU:** Intel Core i7-10750H
- **RAM:** 64GB DDR4
- **Storage:**
  - Samsung 980 500GB NVMe SSD (Primary/Boot)
  - 1TB HDD (Data/VM Storage)

## What Was Accomplished
- Installed Proxmox VE 9.2.2
- Configured dual storage — NVMe for OS, HDD as DataHDD volume group
- Created Ubuntu Server VM (2 cores, 2GB RAM, 50GB disk)
- Installed Docker on Ubuntu Server
- Deployed Jellyfin media server (http://x.x.x.x:8096)
- Deployed Nextcloud personal cloud storage (http://x.x.x.x:8080)
- Deployed Nginx Proxy Manager (http://x.x.x.x:81)
- Enabled fstrim.timer for SSD health optimization

## Challenges & Resolutions

### 1. Boot Configuration Conflict
- **Problem:** Post-installation, the Proxmox host failed to boot.
- **Diagnosis:** The system was attempting to boot from an existing OS 
partition on the secondary HDD rather than the new Proxmox installation 
on the NVMe SSD.
- **Resolution:** Temporarily removed the HDD, wiped all partitions to 
clear the boot sector, and reinstalled the drive. This forced the system 
to correctly boot from the NVMe.

### 2. Network Connectivity & DNS Misconfiguration
- **Problem:** Proxmox was not reachable via the web GUI; the terminal 
was restricted to IPv6, and standard network commands returned 
"temporary failure in name resolution."
- **Diagnosis:** The host failed to negotiate an IPv4 address during install.
- **Resolution:**
  - Audited the network gateway using `ip route`
  - Updated `/etc/network/interfaces` to define a static IPv4 address 
(x.x.x.x) and added Google DNS (8.8.8.8, 8.8.4.4)
  - Validated connectivity via ICMP echo to `8.8.8.8`, confirming the 
physical layer was functional

### 3. APT Repository Errors
- **Problem:** Proxmox enterprise repositories blocking updates without 
a paid subscription
- **Resolution:** Disabled enterprise repos via the Proxmox web UI and 
added the free pve-no-subscription community repository

## Services Running
| Service | URL | Purpose |
|---|---|---|
| Proxmox | https://x.x.x.x:8006 | Hypervisor management |
| Jellyfin | http://x.x.x.x:8096 | Personal media server |
| Nextcloud | http://x.x.x.x:8080 | Personal cloud storage |
| Nginx Proxy Manager | http://x.x.x.x:81 | Reverse proxy |

## Next Steps
- Deploy pfSense firewall VM
- Deploy Windows Server with Active Directory
- Deploy Kali Linux attack VM
- Deploy Metasploitable vulnerable target VM
- Implement Wazuh SIEM for centralized monitoring



