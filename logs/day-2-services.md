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
- Identified media storage on Nobara PC
- Transferred movies, music, and TV shows via rsync over local network
- Verified all files landed correctly on the 1TB HDD partition

### Jellyfin Configuration
- Recreated Jellyfin container with /mnt/media mounted as /media
- Added three media libraries:
  - Movies → /media/movies
  - Music → /media/music
  - TV Shows → /media/tvshows
- Jellyfin scanned and pulled metadata and artwork automatically
- Verified media accessible from MacBook and iPhone via browser

### Nextcloud Fix
- Diagnosed Internal Server Error caused by SQLite database
  permissions conflict after adding HDD data mount
- Resolution: full clean reinstall of Nextcloud container
- Set www-data ownership on /mnt/media/nextcloud
- Recreated container with:
  - App files on SSD
  - Data files on HDD at /mnt/media/nextcloud
- Verified data storage by uploading test file and confirming
  it appeared in the correct data directory

### Monitoring & Management
- Installed Portainer — visual Docker container management
- Installed Watchtower — auto-updates all containers daily at 4am
- Installed Uptime Kuma — service health monitoring
- Configured Discord webhook for downtime alerts
- Added monitors for all services

## Services Running
| Service | Storage |
|---|---|
| Proxmox | - |
| Jellyfin | 1TB HDD |
| Nextcloud | 1TB HDD |
| Nginx Proxy Manager | - |
| Portainer | - |
| Uptime Kuma | - |
| Watchtower | background |

## Challenges & Resolutions

### 1. Nextcloud Internal Server Error
**Problem:** After adding HDD data mount, Nextcloud threw 500 errors.
**Diagnosis:** SQLite database permissions conflict between old
config and new data path.
**Resolution:** Full clean reinstall with correct www-data ownership
on data directory.

### 2. Container Permissions
**Problem:** Newly created folders owned by root, causing
permission denied errors.
**Resolution:** Used chown to transfer ownership to correct users —
system user for general folders, www-data for Nextcloud.

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
