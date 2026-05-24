# Day 2 — Media Setup, Storage Configuration & Monitoring
Date: May 24, 2026

## Objective
Configure proper storage for media and cloud services, deploy monitoring 
tools, and get Jellyfin and Nextcloud fully operational with data stored 
on the 1TB HDD.

## What I Did

### Storage Expansion
- Added a 300GB virtual disk to the Ubuntu Server VM in Proxmox
- Formatted the disk as ext4 and mounted it at /mnt/media
- Added to /etc/fstab for automatic mount on reboot
- Created organized folder structure:
  - /mnt/media/movies
  - /mnt/media/music
  - /mnt/media/tvshows
  - /mnt/media/nextcloud

### Media Transfer
- Identified media storage on Nobara PC at /mnt/9A3A2A983A2A7185
- Transferred movies, music, and TV shows via rsync over local network
- Verified all files landed correctly on the 1TB HDD partition

### Jellyfin Configuration
- Recreated Jellyfin container with /mnt/media mounted as /media
- Added three media libraries:
  - Movies → /media/movies
  - Music → /media/music
  - TV Shows → /media/tvshows
- Jellyfin scanned and pulled metadata and artwork automatically
- Verified media accessible from MacBook and iPhone via Chrome

### Nextcloud Fix
- Diagnosed Internal Server Error caused by SQLite database 
  permissions conflict after adding HDD data mount
- Resolution: full clean reinstall of Nextcloud container
- Set www-data (uid 33) ownership on /mnt/media/nextcloud
- Recreated container with:
  - App files: /home/<admin-user>/nextcloud → /var/www/html (SSD)
  - Data files: /mnt/media/nextcloud → /var/www/html/data (HDD)
- Verified data storage by uploading test file and confirming 
  it appeared in /mnt/media/nextcloud/<admin-user>/files/

### Monitoring & Management
- Installed Portainer (port 9000) — visual Docker container management
- Installed Watchtower — auto-updates all containers daily at 4am
- Installed Uptime Kuma (port 3001) — service health monitoring
- Configured Discord webhook for downtime alerts
- Added monitors for all 5 services

## Services Running
| Service | URL | Storage |
|---|---|---|
| Proxmox | https://x.x.x.x:8006 | - |
| Jellyfin | http://x.x.x.x:8096 | 1TB HDD |
| Nextcloud | http://x.x.x.x:8080 | 1TB HDD |
| Nginx Proxy Manager | http://x.x.x.x:81 | - |
| Portainer | http://x.x.x.x:9000 | - |
| Uptime Kuma | http://x.x.x.x:3001 | - |
| Watchtower | background | - |

## Challenges & Resolutions

### 1. Nextcloud Internal Server Error
- **Problem:** After adding HDD data mount, Nextcloud threw 500 errors
- **Diagnosis:** SQLite database permissions conflict between old 
  config and new data path
- **Resolution:** Full clean reinstall with correct www-data ownership 
  on data directory

### 2. Container Permissions
- **Problem:** Newly created folders owned by root, causing 
  permission denied errors
- **Resolution:** Used chown to transfer ownership to correct users 
  (<admin-user> for general folders, www-data uid 33 for Nextcloud)

## What I Learned
- Docker volume mounts and how data persists outside containers
- Container user permissions and why ownership matters
- How to diagnose container issues using docker logs
- rsync for efficient file transfers over a local network
- How to verify data is landing on the correct physical drive

## Next Steps
- Deploy pfSense firewall VM
- Deploy Windows Server 2022 with Active Directory
- Deploy Kali Linux attack VM
- Deploy Metasploitable vulnerable target
- Implement Wazuh SIEM
