# 2026-07-01 — Public Repo Security Audit & History Remediation

Audited all public GitHub repos (Homelab-Project, pi-survival-server,
nobara-ai-stack) for sensitive data exposure — scanning not just current
files, but the full git history of every repo.

## Findings

### 1. Full internal network map in git history (High)
Current files had been sanitized in an earlier commit, but the original
commits were still present. `git log -p` exposed the complete unmasked
service table: hypervisor, Docker host, and security lab VM IPs with
ports, plus pfSense WAN/LAN layout and DHCP ranges. Anyone cloning the
repo could reconstruct the entire internal topology.

**Lesson:** editing a file only fixes the latest version. Git preserves
every prior version by design — sanitizing history requires rewriting it.

### 2. Live service username in a current log (Medium)
A password-reset command in a recovery log contained a real account
username for a publicly reachable service — half a credential pair for
anyone attempting password spraying.

### 3. Inconsistent IP masking across repos (Low)
One repo followed the masking convention; another had a raw LAN address
in a current file.

### 4. Documented passphrase-less SSH key (Medium)
A log publicly stated that the SSH private key had its passphrase removed
for convenience — both a weak practice and an unnecessary disclosure.

## Remediation

- Installed `git-filter-repo` and built replacement rule files
  (`--replace-text`) with literal and regex rules to rewrite sensitive
  strings across **every commit** in history
- Masked all private IPv4 addresses and the exposed username repo-wide
- Rewrote SSH key disclosure lines in all historical commits, then
  force-pushed the cleaned history to GitHub
- Ran multiple passes: the first regex missed partially-masked addresses
  (e.g. `x`-suffixed octets), and manual sanitization commits initially
  advertised the removed content in their own diffs — both fixed by
  additional filter-repo passes so no version of history ever contained
  the sensitive text
- Hardened the actual key: added a passphrase via `ssh-keygen -p` and
  configured `AddKeysToAgent yes` in `~/.ssh/config` — same seamless
  VS Code/SSH workflow, but a stolen key file is now useless

## Verification

- Fresh clone from GitHub after each push (verifying the remote state,
  not the local copy)
- `git log --all -p` piped through `grep -E` patterns for private IP
  ranges, usernames, tokens, key material, and credential keywords —
  all returned clean
- Manually spot-checked oldest commits in the GitHub web UI

## What I Learned

- Git history is an attack surface. Public repo hygiene means auditing
  `git log -p`, not just the file tree
- `git filter-repo --replace-text` rewrites content across all commits
  while preserving messages and dates; it detaches the remote as a
  safety measure, so re-add origin before force-pushing
- Replacement rules apply top-down — exception rules go first
- A "remove sensitive line" commit leaks that line in its own diff;
  true removal means rewriting history so the line never existed
- After a force push, every stale clone on other machines must be
  deleted and re-cloned, or the dirty history can be pushed back
- Verify from the attacker's perspective: clone fresh from the public
  remote and scan that

## Next Steps

- Delete and re-clone stale copies of these repos on all other machines
- Periodic re-audit after major documentation pushes
- Keep exact software versions out of public logs where practical
