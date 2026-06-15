# 2026-06-12 — Media Automation Stack (Prowlarr, Radarr, Sonarr, Jellyseerr, qBittorrent)

## Objective
Stand up an automated media management stack in front of the existing
Jellyfin server to organize and manage a large personal physical media
collection being digitized. Wire the components together so requests flow
end to end: request → manage → download → stream.

## Architecture
The stack feeds the existing Jellyfin instance:

  Jellyseerr (request UI)
      → Radarr (movies) / Sonarr (TV)
          → qBittorrent (download client)
              → Jellyfin (library / streaming)

Prowlarr sits to the side as the indexer manager, syncing indexers into
both Radarr and Sonarr so they don't have to be configured individually.

## What Was Accomplished

### Containers Deployed (Docker on Ubuntu Server)
- qBittorrent (linuxserver image) — download client, web UI on port 8085
- Prowlarr — indexer manager, port 9696
- Radarr — movie management, port 7878
- Sonarr — TV management, port 8989
- Jellyseerr — request frontend, port 5055
- Flaresolverr — added on port 8191 to solve indexer challenges

### qBittorrent
- Set default save path
- Created categories mapping to library folders:
  - movies → /media/movies
  - tvshows → /media/tvshows
- Routed traffic through NordVPN (SOCKS5 proxy), with "resolve hostnames
  through proxy" enabled so DNS doesn't leak around the VPN

### Prowlarr
- Added indexers
- Connected Prowlarr to Radarr (Apps)
- Connected Prowlarr to Sonarr (Apps)
- Indexers now sync automatically into both — no per-app indexer setup

### Radarr
- Root folder: /movies
- Download client: qBittorrent (host = Ubuntu Server, port 8085,
  category "movies")
- Indexers synced automatically from Prowlarr

### Sonarr
- Root folder: /tv
- Download client: qBittorrent (port 8085, category "tvshows")
- Indexers synced automatically from Prowlarr

### Jellyseerr
- Signed in using Jellyfin credentials (single sign-on with the library)
- Connected Radarr (host, port 7878, API key from Radarr → Settings → General)
- Connected Sonarr (host, port 8989, API key from Sonarr → Settings → General)
- Provides a Netflix-style browse/request interface in front of the stack

## Challenges & Resolutions

### 1. Sonarr/Radarr Couldn't Write to Root Folder
**Problem:** Sonarr threw "Folder '/tv/' is not writable by user 'abc'"
when adding the root folder.
**Diagnosis:** The linuxserver containers run as PUID 1000 (user "abc"
inside the container), but the media folders were owned by a different user.
**Resolution:**
  sudo chown -R 1000:1000 /mnt/media/tvshows
  sudo chown -R 1000:1000 /mnt/media/movies
After fixing ownership, the root folders added successfully.

### 2. Whether Prowlarr Needs a Download Client
**Question:** Does a download client need to be added to Prowlarr?
**Answer:** No. Prowlarr only manages indexers and syncs them to Radarr
and Sonarr. The download client (qBittorrent) is configured in Radarr and
Sonarr directly, not in Prowlarr.

## What I Learned
- The *arr stack is a pipeline of single-purpose services, each doing one
  job and handing off to the next — classic separation of concerns
- Prowlarr centralizes indexer management so indexers are configured once
  and synced everywhere, instead of per-app
- linuxserver containers run as PUID/PGID 1000 ("abc" internally) — host
  folder ownership must match or the container can't write
- Download clients belong in Radarr/Sonarr, not Prowlarr
- Routing the download client through the VPN's SOCKS5 proxy (with proxy
  DNS resolution) keeps that traffic isolated from the rest of the homelab

## Next Steps
- Confirm Jellyfin library scans pick up completed downloads automatically
- Verify the end-to-end flow with a request through Jellyseerr
- Document the stack's ports in the homelab reference
- Continue the n8n email triage pipeline (separate track)
