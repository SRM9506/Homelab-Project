# 2026-06-04 — Security Lab, Wazuh SIEM & First Attack/Defense Exercise

## Objective
Deploy Wazuh SIEM, connect agents to all homelab machines, and conduct
first red team / blue team exercise using Kali Linux against Windows
Server and Metasploitable.

## What Was Accomplished

### Metasploitable2 Deployment
- Imported Metasploitable2 VMDK into Proxmox as VM 104
- Configured static IP on main network subnet
- Took clean baseline snapshot before any attacks
- Confirmed 22 open ports via Nmap including critical vulnerabilities:
  - vsftpd 2.3.4 (backdoored version)
  - Port 1524 open root shell
  - UnrealIRCd (backdoored)
  - Samba, MySQL, VNC, Tomcat all exposed

### Wazuh SIEM Deployment
- Created dedicated Ubuntu 24.04.4 VM (VM 105) for Wazuh
  - 4 cores, 8GB RAM, 50GB disk on local-lvm
  - Static IP configured via netplan
- Installed Wazuh 4.9.2 all-in-one using official install script
  - Wazuh Manager, Indexer, and Dashboard all installed
- Fixed disk flood-stage watermark by expanding LVM partition to 48GB
- Changed admin password to a memorable password
- Deployed Wazuh agents on:
  - Ubuntu Server — DEB package
  - Kali Linux — DEB package  
  - Windows Server — MSI package, started via NET START WazuhSvc
- All 3 agents confirmed active in Endpoints Summary

### Active Directory Lab Completion
- Created 5 Organizational Units: IT Users, IT Admins, Workstations,
  Servers, Security Groups
- Created 5 users: jsmith, sjohnson, mdavis, shakil.admin, svc_sql
- Created 3 security groups: IT Helpdesk, IT Admins, File Share Users
- Set SPN on svc_sql for Kerberoasting practice
- Created 6 SMB shares with realistic permissions and fake sensitive files
- Configured IT Security Baseline GPO linked to domain
- Enabled full audit policy for all security event categories
- Configured intentional misconfigurations for attack practice:
  - svc_sql added to Domain Admins
  - mdavis password never expires
  - jsmith reversible encryption enabled
  - SMB signing disabled
  - Backup share with everyone full access and credential file
- Took snapshot: ad-lab-complete

### First Red Team / Blue Team Exercise

**Reconnaissance:**
- Ran Nmap SYN scan with version detection against Metasploitable
- Identified 22 open ports and vulnerable service versions
- Discovered open root shell on port 1524

**Brute Force Attack:**
- Used Hydra to brute force Administrator account via RDP on Windows Server
- Attack generated hundreds of failed login attempts per minute

**Wazuh Detection:**
- Rule 60204 fired — Multiple Windows Logon Failures
- Alert level 10 (high severity)
- MITRE ATT&CK T1110 (Brute Force) automatically mapped
- Forensic evidence captured:
  - Attacker IP and hostname (kali)
  - Target account (Administrator)
  - Authentication method (NTLM)
  - Status codes confirming valid username, wrong password
  - Compliance frameworks auto-tagged: PCI DSS, HIPAA, NIST, GDPR
- Rule fired 53+ times during the attack

## What I Learned
- How to deploy and configure Wazuh SIEM in a homelab environment
- How Wazuh agents collect and forward logs to the manager
- How Windows Event ID 4625 (logon failure) maps to brute force detection
- How MITRE ATT&CK framework maps to real attack techniques
- How status codes 0xC000006D/0xC000006A reveal account enumeration
- How a SOC analyst reads and interprets security alerts
- The full red team / blue team workflow — attack and detect simultaneously

## Next Steps
- Kerberoasting — request and crack svc_sql Kerberos ticket
- vsftpd 2.3.4 exploit — gain root shell on Metasploitable via Metasploit
- BloodHound — map AD attack paths visually
- Pass the hash attacks
