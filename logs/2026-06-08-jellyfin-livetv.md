# 2026-06-09 — Jellyfin Live TV, EPG Cleanup & Multi-Stream Verification

## Objective

Clean up the Jellyfin Live TV guide data, confirm the homelab server can
handle simultaneous streams to multiple devices, and document multi-user
setup.

## What Was Accomplished

### EPG / Guide Data Cleanup

- Removed unused PlutoTV XML guide source from the Live TV configuration
- Kept only the iptv-org US guide to match the active US channels playlist
- Guide data now aligned with the actual channel lineup — no orphaned
  or mismatched program data

### Multi-Stream Verification

- Confirmed the Jellyfin server (Docker on Ubuntu Server) handles multiple
  independent streams simultaneously
- Verified one user can watch on PC while another watches different content
  on the TV
- Streams use direct play where possible — the i7 handles concurrent
  playback comfortably without transcoding strain

### Multi-User Setup

- Confirmed adding a separate Jellyfin user account per viewer gives each:
  - Their own watch history
  - Their own continue-watching list
  - Optional parental controls

## What I Learned

- How Jellyfin EPG sources map to channel playlists, and why stale guide
  sources should be removed to keep program data accurate
- Jellyfin is built for concurrent multi-client streaming from a single
  server — different content to different devices at the same time
- Direct play avoids transcoding overhead; transcoding is only triggered
  when a client can’t play the source format natively
- Per-user accounts isolate watch history and enable parental controls

## Next Steps

- Monitor server load during simultaneous transcoded streams (vs direct play)
- Consider hardware transcoding options if concurrent transcoding becomes
  a bottleneck