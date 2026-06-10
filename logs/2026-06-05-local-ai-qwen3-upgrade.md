# 2026-06-05 — Local AI Model Upgrade & SSD Migration

## Objective

Upgrade the daily-driver local AI model to a newer release with more recent
training data, confirm it loads in Open WebUI, and move it onto the NVMe SSD
for faster load times.

## What Was Accomplished

### Model Upgrade — Qwen3

- Reviewed existing models (llama3.1:8b, codellama:7b, qwen2.5:14b) — all
  with training data ending around end of 2023
- Identified Qwen3 as the upgrade over qwen2.5:14b — newer release, more
  recent training data, comparable performance within the 12GB VRAM budget
- Pulled qwen3:14b (~9.3GB) via Ollama
- Deleted the old qwen2.5:14b to free space
- Confirmed all three models (qwen3:14b, codellama:7b, llama3.1:8b) showing
  in `ollama list` and in the Open WebUI model selector

### Moving Qwen3 to the NVMe SSD

- Goal: store the daily-driver model on the NVMe instead of the HDD for
  faster load times
- Configured Ollama to use the NVMe path via a symlink from the default
  models directory, plus a systemd service override
- Set `OLLAMA_HOST=0.0.0.0` and pointed `OLLAMA_MODELS` at the NVMe path
  in the override

## Challenges & Resolutions

### 1. Ollama Service User Couldn’t Access Home Directory

**Problem:** The first attempt stored models under the user home directory,
but the `ollama` service user couldn’t traverse it — permission denied.
**Resolution:** Used a symlink from Ollama’s own default path
(`/usr/share/ollama/.ollama/models`) to the NVMe location so the service
user never hits a permission wall, and added `chmod o+x` on the home
directory so the ollama user can traverse it.

### 2. Going in Circles Confirming State

**Problem:** Lost track of which models were pulled/deleted and whether they
were showing in Open WebUI during the session.
**Resolution:** Used `ollama list` as the single source of truth to confirm
exactly what was installed before making further changes.

## What I Learned

- How to evaluate local models by training-data recency, not just size
- Newer mid-size models (Qwen3) can match or beat older larger ones within
  the same VRAM budget
- The correct way to relocate Ollama model storage on Linux is a symlink
  from the default path plus a systemd override — not stuffing models in a
  home directory the service user can’t reach
- Always verify state with `ollama list` before stacking more changes

## Next Steps

- Confirm load-time improvement from NVMe vs HDD
- Set Qwen3 as the default model in Open WebUI
- Evaluate whether a smaller always-on model is worth running on the homelab
  itself for automation tasks