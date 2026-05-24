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

### Virtualization & Services
- Proxmox VE installed and configured with dual storage pool
- Ubuntu Server VM deployed (Docker host)
- Jellyfin media server — `http://x.x.x.x:8096`
- Nextcloud personal cloud storage — `http://x.x.x.x:8080`
- Nginx Proxy Manager — `http://x.x.x.x:81`

---

## 🗺️ Roadmap

### Networking & Security
- [ ] pfSense — firewall rules and inter-VLAN routing
- [ ] Kali Linux VM — offensive security practice
- [ ] Metasploitable — vulnerable target for penetration testing
- [ ] DVWA — vulnerable web application practice
- [ ] Wazuh SIEM — centralized monitoring and threat detection

### Enterprise Identity
- [ ] Windows Server 2022 — Active Directory implementation
- [ ] Group Policy configuration
- [ ] AD attack and defense practice

### Cloud & DevOps
- [ ] Docker Compose for service management
- [ ] CI/CD pipeline for automated deployments
- [ ] Terraform infrastructure as code practice

---

## 📁 Repository Structure
homelab-project/
├── README.md
└── logs/
    └── day-1-setup.md

## 📋 Project Philosophy
I treat this lab as a production environment. I document my progress, 
troubleshoot systematically, and maintain a security-first architecture 
to prepare for a career in cybersecurity and cloud engineering.

---

## 📊 Skills Being Developed
`Proxmox` `Linux` `Docker` `Networking` `pfSense` `Active Directory` 
`Penetration Testing` `SIEM` `Cloud Infrastructure` `DevOps`
