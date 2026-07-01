# Day — Homepage Fix, Port Audit, Media Stack & Immich ML Diagnosis
Date: July 1, 2026

## Objective
Recover the Homepage dashboard after an auto-update broke it, perform a full
port audit of the running Docker stack, revert the dashboard to local
IP:port links, and diagnose two separate Immich display issues.

## What Was Accomplished
- Restored the Homepage dashboard after a Watchtower auto-update broke host validation
- Completed a full port audit via `docker ps`; documented the previously
  undocumented *arr media-automation stack
- Reverted the Homepage `services.yaml` to local IP:port links, regrouped, and
  added all new services
- Diagnosed two independent Immich issues (missing thumbnails + ML container flapping)
- Updated internal documentation (Notion + Obsidian) to reflect current state

## Challenges & Resolutions

### 1. Homepage — Host Validation Failure
**Problem:** Dashboard returned `Host validation failed. See logs for more details.`
**Diagnosis:** An unattended container update pulled Homepage v1.0, which introduced
the required `HOMEPAGE_ALLOWED_HOSTS` variable. The container was recreated across
the breaking change and lost the previously-set allowed-hosts value.
**Resolution:** Recreated the container with the variable restored (env vars are set
at container creation, so a restart does not apply them). Config lives in a host
bind mount, so remove/recreate left all dashboard settings intact.
**Follow-up:** Pin Homepage to a version tag instead of `latest` during the Compose
migration so unattended updates cannot wipe config again.

### 2. Undocumented Services Discovered
**Problem:** Running stack had drifted from documentation.
**Diagnosis:** `docker ps` revealed a full *arr media-automation stack and a workflow
automation tool that were never logged.
**Resolution:** Documented all services and confirmed ports. Media flow:
indexer manager -> movie/TV automation -> torrent client -> media server.

### 3. Immich — Thumbnails Show "Error Loading Image"
**Problem:** Grid thumbnails failed to load, but originals opened fine on click.
**Diagnosis:** Thumbnail generation did not keep pace with the large initial import.
Originals are served directly; the separate generated thumbnail files were missing.
**Resolution:** Administration -> Jobs -> run Generate Thumbnails (All). This is a
server-side job with no machine-learning dependency.

### 4. Immich — Machine-Learning Container Flapping
**Problem:** ML container cycled healthy/unhealthy; server logged `fetch failed` and
`Machine learning request ... failed for all URLs`.
**Diagnosis:** Not an out-of-memory event (`OOMKilled: false`) and not a crash. Models
load correctly but run on `CPUExecutionProvider`. CPU inference is too slow under batch
load, so requests time out and the server marks ML unhealthy.
**Resolution (immediate):** Set Smart Search and Face Detection concurrency to 1 to stop
the timeout storm; let thumbnail/metadata jobs finish so the library is browsable.
**Resolution (long-term):** Move Immich ML to GPU using the CUDA image, ideally hosted on
the GPU workstation, converting multi-second CPU passes to sub-second GPU passes.

## What I Learned
- Containers are disposable; state lives in bind mounts and named volumes. Changing an
  env var means remove + recreate, not restart — and it is safe because data is external.
- Letting containers ride `latest` with an auto-updater is how a working service breaks
  overnight. Pin versions for anything whose config matters.
- Undocumented drift is real — a periodic `docker ps` audit catches services that never
  made it into the docs.
- "Error loading image" in Immich is almost always thumbnail generation, not the originals.
- Immich ML runs on CPU by default; GPU acceleration is the fix for timeout/throughput
  issues, not more RAM.

## Next Steps
- [ ] Docker Compose migration — backup of config dirs + named volumes prepared;
      auto-generate the compose file, then cut over one stack at a time and pin versions
- [ ] Move Immich ML to GPU (CUDA image on the GPU workstation)
