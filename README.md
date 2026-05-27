# 🖥️ Home Lab: Infrastructure & Cloud Sandbox

## Purpose
This repository documents my self-hosted home lab environment, built to 
bridge the gap between hobbyist IT and professional-grade systems 
engineering. Every service, configuration, and troubleshooting step is 
documented to demonstrate real hands-on experience.

---

## 🔧 Hardware Infrastructure

| Component | Spec |
|---|---|
| Host | MSI GF75 Thin (10UEK-048) |
| CPU | Intel Core i7-10750H (6C/12T) |
| RAM | 64GB DDR4 3200MHz |
| Boot Drive | Samsung 980 500GB NVMe SSD |
| Data Drive | 1TB HDD |
| GPU | NVIDIA RTX 3060 6GB |
| Hypervisor | Proxmox VE 9.2.2 |

---

## ✅ Completed

### Virtualization & Core Infrastructure
- Proxmox VE 9.2.2 installed with dual storage pool (NVMe + HDD)
- pfSense CE 2.7.2 firewall VM deployed and configured
- Ubuntu Server VM deployed as primary Docker host
- Kali Linux VM deployed with XFCE desktop and RDP access

### Networking & DNS
- pfSense WAN/LAN interfaces configured
- Local DNS resolver with host overrides for all services
- Nginx Proxy Manager routing all services by domain name
- All services accessible via clean .lan domains
- Router DNS set to pfSense for network-wide resolution
- Static IPs configured on all VMs permanently

### Remote Access
- Cloudflare Tunnel deployed via Docker on Ubuntu Server
- All services publicly accessible via mondol.dev subdomains with HTTPS
- No port forwarding required — outbound tunnel bypasses ISP restrictions
- Verified working on cellular from outside home network

### Self-Hosted Services
- Jellyfin media server — `http://jellyfin.lan` / `https://jellyfin.mondol.dev`
- Nextcloud personal cloud storage — `http://nextcloud.lan` / `https://nextcloud.mondol.dev`
- Nginx Proxy Manager — `http://npm.lan`
- Portainer Docker management — `http://portainer.lan` / `https://portainer.mondol.dev`
- Uptime Kuma service monitoring — `http://uptime.lan`
- Homepage dashboard — `http://home.lan` / `https://home.mondol.dev`
- Pi-hole ad blocker — `http://x.x.x.x:8053/admin`
- Minecraft Bedrock server — `x.x.x.x:19132`
- Watchtower — automated daily container updates
- Cloudflared — Cloudflare Tunnel connector

### Storage
- 300GB virtual disk mounted at /mnt/media on 1TB HDD
- Jellyfin media library (movies, music, TV) stored on HDD
- Nextcloud data stored on HDD
- Proper ownership and permissions configured

### Security Lab
- Kali Linux 2026.1 VM with full tool suite
- xrdp configured for full remote desktop access
- Static IP assigned at x.x.x.x
- SSH access enabled

### Professional Presence
- Portfolio website live at https://mondol.dev
- Deployed via Cloudflare Pages with auto-deploy from GitHub
- LinkedIn updated with IT/cybersecurity career direction
- GitHub homelab repo with daily documentation logs

---

## 🗺️ Roadmap

### Networking & Security
- [x] pfSense — firewall rules and DNS configuration
- [x] Kali Linux VM — offensive security practice
- [x] Remote access via Cloudflare Tunnel
- [ ] pfSense VLANs — network segmentation
- [ ] WireGuard VPN (blocked by Verizon Fios — using Cloudflare Tunnel instead)
- [ ] Metasploitable — vulnerable target for penetration testing
- [ ] DVWA — vulnerable web application practice
- [ ] Wazuh SIEM — centralized monitoring and threat detection

### Enterprise Identity
- [ ] Windows Server 2022 — Active Directory implementation
- [ ] Group Policy configuration
- [ ] AD attack and defense practice

### Cloud & DevOps
- [x] Docker for service management
- [ ] Docker Compose migration
- [ ] CI/CD pipeline for automated deployments
- [ ] Terraform infrastructure as code practice
---

## 📁 Repository Structure
homelab-project/
├── README.md
└── logs/
├── day-1-setup.md
├── day-2-services.md
└── day-3-pfsense.md

---

## 🌐 Active Services

| Service | URL | Purpose |
|---|---|---|
| Homepage | http://home.lan | Dashboard |
| Proxmox | https://x.x.x.x:8006 | Hypervisor |
| pfSense | https://pfsense.lan | Firewall |
| Jellyfin | http://jellyfin.lan | Media Server |
| Nextcloud | http://nextcloud.lan | Cloud Storage |
| Portainer | http://portainer.lan | Docker Management |
| Uptime Kuma | http://uptime.lan | Monitoring |
| Nginx Proxy Manager | http://npm.lan | Reverse Proxy |
| Pi-hole | http://x.x.x.x:8053/admin | Ad Blocker |
| Kali Linux | x.x.x.x:3389 (RDP) | Security Lab |
| Minecraft Bedrock | x.x.x.x:19132 | Game Server |

---

## 📋 Project Philosophy
I treat this lab as a production environment. I document my progress, 
troubleshoot systematically, and maintain a security-first architecture 
to prepare for a career in cybersecurity and cloud engineering.

---

## 📊 Skills Being Developed
`Proxmox` `Linux` `Docker` `Networking` `pfSense` `Kali Linux`
`Active Directory` `Penetration Testing` `SIEM` `DNS` `Reverse Proxy`
`Cloud Infrastructure` `DevOps` `RDP` `Firewall Configuration`
