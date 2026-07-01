# 2026-06-10 — VS Code Remote Setup, Ollama CPU Classifier, n8n Automation & Telegram Bot

## Objective
Set up VS Code as a remote development environment for the homelab, deploy
a CPU-based AI classifier on Ubuntu Server, stand up n8n as a self-hosted
automation platform, and build the first notification workflow via Telegram.

## What Was Accomplished

### VS Code Remote SSH Setup
- Installed VS Code on MacBook and set up Remote SSH extension
- Configured SSH config file with ubuntu-server as a named host
- Generated an ed25519 SSH key on the MacBook and copied the public key
  to Ubuntu Server's authorized_keys manually
- Enabled PubkeyAuthentication in sshd_config (was commented out by default)
- Set correct permissions on ~/.ssh and authorized_keys (700 and 600)
- Installed extensions: Remote SSH, Docker, YAML, GitLens, Markdown All in One
- GitHub Copilot available via free trial — tested Chat and Agent modes

### Ubuntu Server VM Resource Upgrade
- Shut down Ubuntu Server VM in Proxmox
- Upgraded RAM allocation from 4GB to 8GB (8192MB)
- Upgraded vCPU allocation from 2 cores to 4 cores
- Booted VM back up — all Docker containers came back online automatically
- Upgrade motivated by upcoming Ollama CPU inference workload and general
  Docker stack headroom

### Ollama CPU-Only Install on Ubuntu Server
- Installed Ollama on Ubuntu Server via official install script
- Hit DNS resolution failure during install — fixed with
  sudo systemctl restart systemd-resolved
- Hit permission denied error on /usr/share/ollama — fixed with:
  sudo mkdir -p /usr/share/ollama && sudo chown -R ollama:ollama /usr/share/ollama
- Ollama service came up active and running after the fix
- Confirmed CPU-only mode — no GPU passed through to this VM by design
- Pulled qwen3:1.7b (~1GB) as the always-on email classifier model
- Confirmed no-think mode via API call with "think": false parameter
- Classification test: fed a sample recruiter email, returned "Recruiter / High"
- Cold start (model loading): ~5 minutes in thinking mode via CLI
- API call with think: false and model already loaded: ~25 seconds
- Decision: no permanent keep-alive — model loads on demand, delay acceptable
  since notifications are background and not time-critical

### Telegram Bot Setup
- Installed Telegram on iPhone
- Created a new bot via @BotFather using /newbot command
- Bot name: Sharkbot 
- Saved bot token and chat ID (retrieved via @userinfobot)
- Confirmed bot is reachable and ready to receive messages from n8n

### n8n Deployment
- Deployed n8n as a Docker container on Ubuntu Server (port 5678)
- Used restart unless-stopped policy with a named volume for persistence
- Added public hostname to Cloudflare Tunnel 
- n8n accessible externally via subdomain 
- Completed n8n account setup and onboarding

### First Workflow — Daily Log Reminder
- Built a Schedule Trigger → Telegram workflow in n8n
- Schedule: daily at 10:30pm
- Telegram node configured with bot token and chat ID credentials
- Message prompts to update the homelab GitHub log for the day
- Tested manually — Telegram notification received on iPhone successfully
- Workflow published and active

## Challenges & Resolutions

### 1. Ollama DNS Resolution Failure
**Problem:** curl couldn't resolve ollama.com during install.
**Resolution:** Restarted systemd-resolved, install completed successfully.

### 2. Ollama Permission Denied on Startup
**Problem:** Ollama service crashed on start — couldn't create
/usr/share/ollama directory, permission denied.
**Resolution:** Manually created the directory and set ownership to the
ollama user. Service came up cleanly after restart.

### 3. VS Code SSH Key Not Working
**Problem:** SSH key authentication kept failing in VS Code despite
ssh-copy-id reporting success. Root cause was PubkeyAuthentication
commented out in sshd_config, and authorized_keys had wrong permissions.
**Resolution:** Uncommented PubkeyAuthentication and AuthorizedKeysFile
in sshd_config, restarted SSH, fixed directory and file permissions,
manually pasted public key into authorized_keys. Also configured ssh-agent
from the key since it was blocking VS Code's non-interactive auth.

### 4. Copilot Agent SSH Connection Failure
**Problem:** GitHub Copilot Agent mode failed to connect to the remote
SSH workspace, throwing a session.create error.
**Resolution:** SSH key setup resolved the auth issue. Agent mode is
functional but occasionally flaky on remote SSH — Chat mode is more
reliable for day-to-day use.

### 5. qwen3:1.7b Thinking Mode Too Slow
**Problem:** Default qwen3 CLI invocation triggered reasoning/thinking
mode, taking over 5 minutes to classify a single email.
**Resolution:** Used the Ollama API directly with "think": false parameter,
bypassing the reasoning chain entirely. Classification time dropped to
~25 seconds on CPU — acceptable for a background notification pipeline.

## What I Learned
- PubkeyAuthentication is commented out by default on Ubuntu 24 — must be
  explicitly enabled for SSH key auth to work
- VS Code Remote SSH spins up its own SSH connection separate from the
  terminal — key-based auth (SSH keys via ssh-agent) is required for
  it to work reliably
- qwen3 is a reasoning model by default — think: false must be passed via
  the API to use it as a fast classifier rather than a slow reasoner
- CPU inference on a 1.7b model is viable for async background tasks where
  a 25-second delay doesn't matter
- n8n v2.0 replaced the Active/Inactive toggle with a Publish button
- Each n8n automation is a self-contained workflow — easy to add, remove,
  or disable independently

## Next Steps
- Build the email triage pipeline in n8n:
  - Gmail read-only OAuth connection
  - Ollama HTTP node for AI classification
  - Filter logic for bills, job updates, recruiters
  - Telegram notification on important emails
- Daily morning digest workflow (batched summary)
- Docker Compose migration of the full stack
- Update homelab GitHub logs consistently after each session
