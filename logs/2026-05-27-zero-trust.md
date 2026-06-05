# 2026-05-27 — Romm, Minecraft Java, Cloudflare Zero Trust & Security Hardening

## Objective
Deploy Romm retro ROM manager, upgrade Minecraft server to support Java
edition via Geyser, implement Cloudflare Zero Trust Access to protect
all public-facing homelab services, and harden the domain against bots
and scrapers.

## What Was Accomplished

### Romm v4.8.1 — Retro ROM Manager
- Deployed Romm Docker container with MariaDB backend
- Registered Twitch developer app at dev.twitch.tv for IGDB API access
- Configured IGDB Client ID and Client Secret as environment variables
- Created correct folder structure at /mnt/media/library/roms/ with
  platform folders: gba, gbc, snes, nes, n64, nds, ps, genesis
- Mounted library folder correctly into container
- Verified IGDB API credentials working via curl OAuth test
- ROM scan working and pulling metadata from IGDB
- Accessible via local .lan domain and external domain
- Added Romm monitor to Uptime Kuma
- Uptime Kuma correctly detected downtime during reinstall and recovery

### Minecraft Java + Geyser Bridge
- Upgraded existing Minecraft Bedrock server to support Java clients
- Enabled ENABLE_GEYSER=true and ENABLE_FLOODGATE=true on container
- Java clients connect via Prism Launcher
- Bedrock clients (iPhone, Xbox, Windows) connect on Bedrock port
- Both editions connect to same world simultaneously
- Homepage updated with two separate entries for Bedrock and Java

### Cloudflare Zero Trust Access
- Enabled Cloudflare Access to protect all public homelab subdomains
- Created individual Access applications for each service
- Authentication method: One-time PIN to Gmail
- Policy: Allow Shakil — email-based allowlist
- All services now require authentication before login page is shown
- Rotated Cloudflare Tunnel token after accidental exposure in terminal

### Proxmox TLS Fix
- Proxmox uses self-signed certificate causing Cloudflare 502 errors
- Fixed by enabling noTLSVerify on the proxmox tunnel route via:
  Zero Trust → Networks → Connectors → Tunnels → homelab →
  Edit → Published application routes → proxmox → Edit →
  Additional application settings → No TLS Verify → On

### Domain Security Hardening
- Enabled Bot Fight Mode in Cloudflare Security settings
- Enabled Block AI Bots → Block on all pages
- Enabled AI Crawl Control — blocked all AI training crawlers
- Enabled DMARC Management to protect domain from email spoofing
- Removed all internal IPs and ports from GitHub public logs
- Replaced raw IPs with .lan domains or local-only references

### Portfolio Updates
- Removed "Days building homelab" stat card
- Updated services count to 13+
- Fixed email display/href mismatch — both now point to custom domain
- Added Cloudflare Tunnel and Zero Trust Access to skills section
- Added Romm and Cloudflare Access to project highlights
- Cloudflare Email Routing configured — custom domain forwards to Gmail

## Services Running
| Service | Local | Public |
|---|---|---|
| Proxmox | proxmox.lan | Protected via Cloudflare Access |
| pfSense | pfsense.lan | Protected via Cloudflare Access |
| Homepage | home.lan | Protected via Cloudflare Access |
| Jellyfin | jellyfin.lan | Protected via Cloudflare Access |
| Nextcloud | nextcloud.lan | Protected via Cloudflare Access |
| Portainer | portainer.lan | Protected via Cloudflare Access |
| Uptime Kuma | uptime.lan | Protected via Cloudflare Access |
| Nginx Proxy Manager | npm.lan | Protected via Cloudflare Access |
| Pi-hole | pihole.lan | Local only |
| Romm | romm.lan | Protected via Cloudflare Access |
| Kali Linux | kali.lan (RDP) | Local only |
| Minecraft Bedrock | Local only | - |
| Minecraft Java | Local only | - |

## Challenges & Resolutions

### 1. Romm IGDB API Key Invalid
**Problem:** Romm returned "API key invalid" despite correct credentials.
**Diagnosis:** IGDB credentials need to be passed as Docker environment
variables, not entered through the web UI.
**Resolution:** Recreated container with IGDB_CLIENT_ID and
IGDB_CLIENT_SECRET as environment variables. Verified credentials
were valid by testing via curl OAuth endpoint first.

### 2. Romm Scan Skipping All Platforms
**Problem:** Scan completed instantly with no ROMs found.
**Diagnosis:** Incorrect folder structure — ROMs were mounted directly
at /romm/library instead of inside a roms subfolder. Official Romm
docs require Structure A: library/roms/{platform}/{game}
**Resolution:** Recreated folder structure at /mnt/media/library/roms/
and mounted parent directory as /romm/library so Romm sees the
roms folder at the correct level.

### 3. Proxmox Bad Gateway via Cloudflare
**Problem:** Proxmox subdomain returned 502 bad gateway.
**Diagnosis:** Cloudflare rejects self-signed TLS certificates by
default. Proxmox uses a self-signed cert.
**Resolution:** Enabled No TLS Verify on the Proxmox tunnel route
via Zero Trust → Networks → Connectors → Tunnels → homelab →
Edit → Published application routes → proxmox → Additional
application settings → No TLS Verify → On.

### 4. Cloudflare Access Login Loop
**Problem:** All subdomains redirecting through one service's auth
endpoint causing login loop.
**Diagnosis:** Grouping all services into one Access application
caused Cloudflare to pick one service as the primary auth domain.
**Resolution:** Deleted grouped application and recreated each
service as its own individual Access application.

### 5. Cloudflare Tunnel Token Exposed
**Problem:** Tunnel token visible in docker inspect output.
**Resolution:** Rotated token immediately via Networking → Tunnels →
homelab → Rotate token. Recreated cloudflared container with new token.

### 6. GitHub IP Exposure
**Problem:** Internal IPs, ports, and usernames publicly visible
in homelab logs on GitHub.
**Resolution:** Audited all log files and README. Replaced all raw
IPs with .lan domain references or local-only placeholders.

## What I Learned
- How Cloudflare Zero Trust Access works and why it's superior to
  relying on service-level authentication alone
- Defense in depth — layering Cloudflare Access in front of services
  that already have their own login adds critical protection
- How IGDB/Twitch API authentication works with OAuth client credentials
- Romm's required folder structure and how Docker volume mounts map
  to internal container paths
- How to rotate compromised credentials immediately
- Importance of auditing public repositories for sensitive information
- How Cloudflare handles TLS verification and self-signed certificates

## Next Steps
- Add romm.lan DNS entry in pfSense host overrides
- Set up Cloudflare browser-based SSH for Ubuntu, Kali, Nobara
- Set up browser-based RDP for Kali and Nobara
- Deploy Windows Server 2022 with Active Directory
- Deploy Metasploitable and DVWA
- Implement Wazuh SIEM
- Fix Pi-hole UDP DNS integration with pfSense
- Set up pfSense VLANs

