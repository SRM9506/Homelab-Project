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
- Windows Server 2022 VM deployed as Active Directory Domain Controller
- Metasploitable2 VM deployed as vulnerable target for security practice
- Wazuh SIEM VM deployed for centralized security monitoring

### Networking & DNS
- pfSense WAN/LAN interfaces configured
- Local DNS resolver with host overrides for all services
- Nginx Proxy Manager routing all services by domain name
- All services accessible via clean .lan domains
- Router DNS set to pfSense for network-wide resolution
- Static IPs configured on all VMs permanently

### Remote Access
- Cloudflare Tunnel deployed via Docker on Ubuntu Server
- All services publicly accessible via custom domain with HTTPS
- Cloudflare Zero Trust Access protecting all public-facing services
- No port forwarding required — outbound tunnel bypasses ISP restrictions
- Verified working on cellular from outside home network
- Browser-based SSH access to Ubuntu Server and Kali via Cloudflare
- Tailscale VPN installed on all devices for secure remote RDP access

### Self-Hosted Services
- Jellyfin media server
- Nextcloud personal cloud storage
- Nginx Proxy Manager
- Portainer Docker management
- Uptime Kuma service monitoring
- Homepage dashboard
- Pi-hole ad blocker
- Minecraft Bedrock + Java server (Geyser bridge)
- Romm retro ROM manager and browser emulator
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
- SSH access enabled on all VMs
- Windows Server 2022 with Active Directory Domain Controller
- Active Directory configured with OUs, users, groups, GPOs
- Intentional AD misconfigurations for attack/defense practice
- Kerberoastable service account (svc_sql with SPN set)
- SMB shares with realistic permissions and sensitive files
- Metasploitable2 vulnerable Linux target deployed
- Wazuh SIEM with agents on Ubuntu Server, Kali, and Windows Server
- First red team / blue team exercise completed:
  - Nmap reconnaissance against Metasploitable
  - Hydra brute force against Windows Server
  - Wazuh detected attack in real time (Rule 60204, Level 10)
  - MITRE ATT&CK T1110 automatically mapped

### Professional Presence
- Portfolio website live at mondol.dev
- Deployed via Cloudflare Pages with auto-deploy from GitHub
- LinkedIn updated with IT/cybersecurity career direction
- GitHub homelab repo with daily documentation logs

---

## 🗺️ Roadmap

### Networking & Security
- [x] pfSense — firewall rules and DNS configuration
- [x] Kali Linux VM — offensive security practice
- [x] Remote access via Cloudflare Tunnel
- [x] Cloudflare Zero Trust Access — protecting all public services
- [x] Metasploitable — vulnerable target for penetration testing
- [x] Wazuh SIEM — centralized monitoring and threat detection
- [ ] Kerberoasting — request and crack service account tickets
- [ ] BloodHound — AD attack path mapping
- [ ] Pass the hash attacks
- [ ] DVWA — vulnerable web application practice
- [ ] pfSense VLANs — network segmentation

### Enterprise Identity
- [x] Windows Server 2022 — Active Directory implementation
- [x] Group Policy configuration
- [x] AD attack and defense practice
- [ ] Join Kali to domain
- [ ] Deploy Windows workstation VM

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
├── 2026-05-23-initial-setup.md
├── 2026-05-24-media-storage.md
├── 2026-05-25-pfsense-dns.md
├── 2026-05-26-remote-access.md
├── 2026-05-27-zero-trust-romm.md
├── 2026-05-28-remote-ssh-rdp.md
├── 2026-06-03-infrastructure-recovery-local-ai.md
└── 2026-06-04-security-lab-wazuh-attack-defense.md

---

## 🌐 Active Services
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
| Minecraft Bedrock + Java | Game Server (Geyser Bridge) |
| Romm | Retro ROM Manager |
| Windows Server 2022 | Active Directory Domain Controller |
| Metasploitable2 | Vulnerable Target (Lab) |
| Wazuh SIEM | Security Monitoring |

---

## 📋 Project Philosophy
I treat this lab as a production environment. I document my progress, 
troubleshoot systematically, and maintain a security-first architecture 
to prepare for a career in cybersecurity and cloud engineering.

---

## 📊 Skills Being Developed
`Proxmox` `Linux` `Docker` `Networking` `pfSense` `Kali Linux`
`Active Directory` `Penetration Testing` `SIEM` `Wazuh` `DNS`
`Reverse Proxy` `Cloud Infrastructure` `DevOps` `RDP` `Tailscale`
`Cloudflare Zero Trust` `Firewall Configuration` `Metasploit`
