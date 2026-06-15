# 🖥️ Home Lab: Infrastructure, Security & Automation Sandbox

## Purpose
This repository documents my self-hosted home lab, built to bridge the gap
between hobbyist IT and professional-grade systems engineering. Every
service, configuration, and troubleshooting step is documented to
demonstrate real, hands-on experience across infrastructure, security,
automation, and local AI.

---

## 🔧 Hardware Infrastructure

| Component | Spec |
|---|---|
| Host | MSI GF75 Thin (10UEK-048) |
| CPU | Intel Core i7-10750H (6C/12T) |
| RAM | 64GB DDR4 3200MHz |
| Boot Drive | Samsung 980 500GB NVMe SSD |
| Data Drive | 1TB HDD |
| GPU | NVIDIA RTX 3060 (Laptop) |
| Hypervisor | Proxmox VE 9.2.2 |

A separate Nobara Linux desktop (RTX 3060 12GB) serves as a dedicated AI and
gaming workstation, kept distinct from the always-on lab.

---

## ✅ Completed

### Virtualization & Core Infrastructure
- Proxmox VE 9.2.2 with dual storage (NVMe + HDD)
- pfSense CE firewall VM
- Ubuntu Server VM as the primary Docker host
- Kali Linux VM with desktop + RDP access
- Windows Server 2022 VM as Active Directory Domain Controller
- Metasploitable2 VM as a vulnerable target for security practice
- Wazuh SIEM VM for centralized security monitoring
- Daily vzdump backups + baseline VM snapshots
- Documented incident recovery: read-only filesystem repair via fsck, and a
  Docker storage-corruption rebuild (lesson: expand VM disks, never migrate
  Docker storage; always shut VMs down cleanly)

### Networking & DNS
- pfSense WAN/LAN configured
- Nginx Proxy Manager routing services by domain name
- Static IPs on all VMs
- Reliable DNS pinned to public resolvers after retiring a half-configured
  local DNS server that was causing intermittent resolution failures

### Remote Access
- Cloudflare Tunnel (Docker on Ubuntu Server) — services public over HTTPS
- No port forwarding required; outbound tunnel bypasses ISP inbound blocking
- Verified working on cellular from outside the home network
- Tailscale on all devices for secure remote SSH, RDP, and app-client access
- VS Code Remote-SSH from MacBook into the lab, with key-based auth

### Self-Hosted Services
- Jellyfin — media server (with Live TV / EPG, multi-stream)
- Nextcloud — personal cloud storage (also backs Obsidian note sync)
- Nginx Proxy Manager — reverse proxy
- Portainer — Docker management
- Uptime Kuma — service monitoring
- Homepage — dashboard
- Minecraft Bedrock + Java (Geyser bridge)
- Romm — retro ROM manager + browser emulator
- Watchtower — automated container updates
- Cloudflared — Cloudflare Tunnel connector

### Media Automation Stack
- Prowlarr — indexer manager (syncs indexers to Radarr/Sonarr)
- Radarr — movie management
- Sonarr — TV management
- Jellyseerr — request frontend (single sign-on with Jellyfin)
- qBittorrent — download client, routed through VPN
- Flaresolverr — indexer challenge solver
- Full pipeline: request → manage → download → Jellyfin library

### Automation & Local AI
- n8n — self-hosted workflow automation, public via Cloudflare Tunnel
  - Daily Telegram log reminder workflow (Schedule → Telegram bot)
  - Daily weather report workflow (Schedule → OpenWeatherMap → Telegram)
  - Email triage pipeline in progress (Gmail → local LLM classify → Telegram)
- Ollama (CPU-only) on Ubuntu Server running a small model (qwen3:1.7b) as
  an always-on email classifier, called via API with reasoning disabled
- Telegram bot for push notifications to phone
- Obsidian note sync across desktop + iPhone via Nextcloud WebDAV
  (Remotely Save), routed over Tailscale to bypass Cloudflare's bot
  challenge on non-browser clients

> Heavier interactive AI (Open WebUI, ComfyUI, Stable Diffusion Forge, larger
> Ollama models) runs on the separate Nobara workstation, not the lab.

### Security Lab
- Kali Linux with full tool suite, xrdp remote desktop, SSH
- Windows Server 2022 Active Directory: OUs, users, groups, GPOs
- Intentional AD misconfigurations for attack/defense practice
  (Kerberoastable svc_sql with SPN, reversible encryption, SMB signing
  disabled, over-permissioned shares with decoy sensitive files)
- Metasploitable2 vulnerable Linux target
- Wazuh SIEM with agents on Ubuntu Server, Kali, and Windows Server
- First red team / blue team exercise:
  - Nmap recon against Metasploitable
  - Hydra brute force against Windows Server
  - Wazuh detected the attack in real time (Rule 60204, Level 10)
  - MITRE ATT&CK T1110 (Brute Force) automatically mapped

### Professional Presence
- Personal site live at mondol.dev — four-page hub (Home, Portfolio, Study
  Guides, Homelab), deployed via Cloudflare Pages with auto-deploy from
  GitHub
- LinkedIn aligned with IT/cybersecurity career direction
- This homelab repo with dated documentation logs

---

## 🗺️ Roadmap

### Automation & Cloud/DevOps
- [x] Docker for service management
- [x] n8n automation platform + first workflows
- [x] Local LLM (Ollama) for workflow classification
- [ ] Finish n8n email triage pipeline (Gmail OAuth → classify → notify)
- [ ] Docker Compose migration of the full stack
- [ ] CI/CD pipeline with a self-hosted GitHub Actions runner
- [ ] Terraform — infrastructure as code
- [ ] Agent sandbox (Dify / Flowise pointed at local Ollama)

### Networking & Security
- [x] pfSense — firewall + DNS
- [x] Kali Linux — offensive security practice
- [x] Remote access via Cloudflare Tunnel
- [x] Metasploitable — vulnerable target
- [x] Wazuh SIEM — monitoring + detection
- [ ] AD attack practice — Kerberoasting, BloodHound, pass-the-hash
- [ ] DVWA — vulnerable web app practice
- [ ] pfSense VLANs — network segmentation

### Enterprise Identity
- [x] Windows Server 2022 — Active Directory
- [x] Group Policy + intentional misconfigurations
- [ ] Join Kali to the domain
- [ ] Deploy a Windows 10/11 workstation VM
- [ ] Deploy a Linux CTF target VM

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
| n8n | Workflow Automation |
| Ollama | Local LLM (classification) |
| Prowlarr / Radarr / Sonarr | Media Automation |
| Jellyseerr | Media Requests |
| qBittorrent | Download Client |
| Kali Linux | Security Lab |
| Minecraft Bedrock + Java | Game Server (Geyser Bridge) |
| Romm | Retro ROM Manager |
| Windows Server 2022 | Active Directory Domain Controller |
| Metasploitable2 | Vulnerable Target (Lab) |
| Wazuh SIEM | Security Monitoring |

---

## 📋 Project Philosophy
I treat this lab as a production environment: I document my progress,
troubleshoot systematically, recover from real incidents, and keep a
security-first mindset to prepare for a career in cybersecurity and cloud
engineering. The `logs/` folder contains dated, session-by-session
write-ups of the work — including the failures and how I fixed them.

---

## 📊 Skills Being Developed
`Proxmox` `Linux` `Docker` `Networking` `pfSense` `Kali Linux`
`Active Directory` `Penetration Testing` `SIEM` `Wazuh` `DNS`
`Reverse Proxy` `Cloud Infrastructure` `DevOps` `RDP` `Tailscale`
`Cloudflare Tunnel` `Firewall Configuration` `Metasploit` `n8n`
`Workflow Automation` `Local LLMs / Ollama` `Incident Recovery`
