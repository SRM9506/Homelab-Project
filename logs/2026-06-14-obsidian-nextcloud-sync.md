# 2026-06-14 — Obsidian Note Sync via Nextcloud WebDAV (Cross-Device)

## Objective
Set up Obsidian as a cross-device note system (desktop + iPhone), synced
through the self-hosted Nextcloud instance using the Remotely Save plugin
over WebDAV — no third-party sync subscription.

## What Was Accomplished

### Desktop Setup
- Installed Obsidian, created a vault
- Installed the Remotely Save community plugin
- Configured Remotely Save to use WebDAV against Nextcloud:
  - Server: Nextcloud WebDAV path (/remote.php/dav/files/USERNAME/)
  - Auth: dedicated Nextcloud app password (not the main account password)

### iPhone Setup
- Installed Obsidian + Remotely Save on the phone
- Pointed it at the same Nextcloud account so notes sync across both devices

## Challenges & Resolutions

### 1. WebDAV Blocked by Cloudflare Bot Challenge
**Problem:** Connecting Remotely Save over the public domain failed. A curl
PROPFIND test returned Cloudflare's "Just a moment..." managed-challenge
page instead of WebDAV XML.
**Diagnosis:** Nextcloud sits behind Cloudflare. The browser passes
Cloudflare's JavaScript challenge automatically, but non-browser WebDAV
clients (curl, Remotely Save) can't run JavaScript, so they get
challenge-walled.
**Attempted fix:** Added a Cloudflare WAF custom rule to Skip managed rules
on the /remote.php path. This downgraded the response from a managed
challenge to a 302 redirect (Browser Integrity Check still firing), but
still didn't let the WebDAV client through cleanly.
**Final resolution:** Pointed Obsidian at Nextcloud over the **Tailscale
IP** instead of the public Cloudflare domain. Tailscale traffic never
touches Cloudflare, so there is no bot challenge to defeat. WebDAV
connected immediately.

### 2. Local IP Wouldn't Load Nextcloud (Phone)
**Problem:** Nextcloud was unreachable from the phone via the server's local
LAN IP, while every other service on that host worked.
**Diagnosis:** The phone was on cellular, not home WiFi — local
x.x.x.x addresses are only reachable on the LAN. (Separately, Nextcloud
enforces a trusted_domains allowlist and host-header validation that other
services don't, which is why it's pickier about which addresses it answers
to.)
**Resolution:** Use the domain or Tailscale IP on the phone, never the
local IP — the phone moves between WiFi and cellular, so only the
always-reachable addresses make sense.

### 3. Vault Name Mismatch
**Problem:** After sync was configured, the iPhone vault wasn't populating.
**Diagnosis:** The vault name on the phone was off by one letter, so
Remotely Save was syncing a different (empty) folder path than the desktop.
**Resolution:** Corrected the vault name to match exactly. Sync populated
immediately.

## What I Learned
- Non-browser clients (WebDAV, API tools) can't pass Cloudflare's
  JavaScript bot challenge — this breaks app clients even when the browser
  works fine
- Cloudflare's free Bot Fight Mode / Browser Integrity Check can't always
  be skipped cleanly with WAF custom rules; fighting it per-client is a
  recurring headache
- Tailscale is the clean path for app clients reaching homelab services:
  it bypasses Cloudflare entirely, so no challenge, no WAF rules. Tradeoff:
  only works when Tailscale is on (fine, since it runs on all my devices)
- Nextcloud enforces trusted_domains + host-header validation as a security
  measure (against host-header injection), which is why it's stricter about
  addresses than other self-hosted services
- Local LAN IPs only work on the LAN — use domain or Tailscale IP on mobile
- Remotely Save syncs by vault name; names must match exactly across devices
- Always use a Nextcloud app password for clients, never the main password

## Next Steps
- Optionally enable auto-sync in Remotely Save so notes sync without manual
  triggering
- Decide whether to add Remotely Save encryption (notes encrypted at rest
  on the server) — would require the same passphrase on every device
- Continue: wire OpenRouter into n8n/Open WebUI; finish n8n email triage
  pipeline; optionally stand up Dify/Flowise as an agent sandbox
