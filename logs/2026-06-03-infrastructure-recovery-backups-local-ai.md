# 2026-06-03 — Infrastructure Recovery, Backups & Local AI

## Objective
Recover from a failed Docker storage migration, establish proper backup
procedures, and set up local AI on Nobara with GPU acceleration.

## What Was Accomplished

### Docker Storage Migration — Failure & Recovery
- Ubuntu Server main disk hit 100% capacity due to Docker image growth
- Attempted to move Docker storage to HDD — caused all container images
  to corrupt
- Correct fix identified: expand VM disk in Proxmox instead
- Expanded Ubuntu Server VM disk from 50GB to 100GB via Proxmox resize
- Ran growpart and resize2fs to expand partition inside Ubuntu
- Main disk now 97GB with 71GB free
- All containers rebuilt from scratch by repulling images
- All volume data was intact — no data loss

### Containers Rebuilt
- Jellyfin — fixed marker conflict by separating config and cache mounts
- Nextcloud — fixed by adding /mnt/media volume mount back
- Nginx Proxy Manager — fixed by correcting data subfolder mount path
- Homepage, Uptime Kuma, Portainer, Cloudflared — restored cleanly
- Minecraft Bedrock+Java — restored with Geyser bridge intact
- Watchtower — fixed API version incompatibility by pinning latest image

### Backup & Snapshot Setup
- Created vzdump backup schedule for Ubuntu Server (daily 2am, keep last 2)
- Took clean baseline snapshots of pfSense, Kali, and Windows Server VMs
- Established standard practice: snapshot before any infrastructure changes

### Local AI — Ollama on Nobara
- Removed Ollama from Ubuntu Server (wrong machine — no GPU)
- Installed Ollama natively on Nobara — detected RTX 3060 automatically
- Pulled llama3.1:8b and codellama:7b models
- Installed Tailscale on Nobara for remote access
- Set up Open WebUI via Docker on Nobara port 3005
- Both models accessible via browser at localhost:3005
- GPU inference confirmed working — fast response times
- Added Nobara to Tailscale network for remote access to Open WebUI

### Local Image Generation
- Installed ComfyUI on Nobara for Stable Diffusion image generation
- Downloaded RealVisXL V4, RealVisXL V5 fp16, and Juggernaut XL models
- Installed Stable Diffusion WebUI Forge as alternative UI
- Fixed Python 3.14 compatibility issues by installing Python 3.10
- Fixed setuptools/pkg_resources breaking change from setuptools v81+
- Created desktop shortcuts for ComfyUI and Forge
- Both UIs running on RTX 3060 with full GPU acceleration

## Challenges & Resolutions

### 1. Docker Migration Caused Container Corruption
**Problem:** Moving Docker data-root to HDD corrupted all container images.
**Resolution:** Expanded VM disk in Proxmox instead — correct solution
from the start. Never move Docker storage; always expand the disk.

### 2. Nginx Proxy Manager Not Loading Config
**Problem:** NPM started fresh instead of loading existing proxy hosts.
**Diagnosis:** Data had moved to /data subfolder during migration.
**Resolution:** Changed volume mount to point to correct subfolder.

### 3. Watchtower API Version Incompatibility
**Problem:** Watchtower reporting client version 1.25 incompatible with
Docker 29.5.2.
**Resolution:** Removed old container, pulled latest image, added
--api-version flag.

### 4. Ollama Out of RAM on Ubuntu Server VM
**Problem:** Ubuntu Server VM only had 2GB RAM — insufficient for 8b model.
**Resolution:** Moved Ollama to Nobara where RTX 3060 handles inference
with GPU acceleration instead of CPU.

### 5. Forge Python Compatibility
**Problem:** Forge requires Python 3.10 but Nobara ships with 3.14.
**Resolution:** Installed python3.10 via dnf, recreated venv with 3.10,
pinned setuptools to 69.5.1 to fix pkg_resources breaking change.

## What I Learned
- Always expand VM disk before running out of space — never migrate storage
- Take Proxmox snapshots before any infrastructure changes
- Docker container images are separate from volume data — data survives
  container deletion when properly mounted
- GPU inference is dramatically faster than CPU for local AI models
- ComfyUI and Forge require specific Python versions and dependency pinning
  on newer Linux distributions

## Next Steps
- Write Active Directory OUs and users
- Deploy Metasploitable and DVWA
- Implement Wazuh SIEM
- Fix Pi-hole UDP DNS

