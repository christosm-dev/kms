# ADR-008: Infrastructure and Hosting

**Date:** 2026-05-06
**Status:** Accepted
**Supersedes:** —

## Context

The project requires a self-hosted, privacy-first infrastructure with no personal knowledge data passing through third-party cloud services. Multiple hosting options were evaluated.

## Decision

**All infrastructure is self-hosted. No cloud services for personal data.**

**NixOS hosts (all managed in `nix-config` repo):**

| Host | Device | Role |
|---|---|---|
| `thinkpad25` | ThinkPad T490s WSL2 | Daily driver development |
| `nixbox` | NixOS server (192.168.0.24) | Vault, API, Obsidian, agent, at-home capture |
| `contabo-vps` | Contabo VPS | Queue, public-facing services |
| `rpi` | Raspberry Pi 4 8GB | Portable away-from-home capture node |
| `laptop` | Linux laptop (future) | Primary Obsidian host, daily driver |

**API codebase:** Single FastAPI codebase deployed on both nixbox and VPS, controlled by `LOGSEQ_ENABLED` config flag:
- nixbox: `LOGSEQ_ENABLED=true` — writes to vault
- VPS: `LOGSEQ_ENABLED=false` — queue only

**VPS API endpoints:**
- `POST /capture` — ingest note
- `GET /queue` — return pending untagged notes (consumed by PWA)
- `DELETE /queue/:id` — mark note processed

**Security:**
- VPS API accessible only over WireGuard VPN (no public port)
- Bearer token authentication on all endpoints
- WireGuard is a prerequisite for v2 away-from-home flow

**Version control:**
- Bare Git repos on nixbox: `vault.git` and `logseq.git`
- Nightly auto-commit via systemd timer
- Gitea web UI deferred to v2

**Explicitly rejected:**
- iCloud — unreliable on Logseq mobile
- Dropbox — privacy concern, personal knowledge data in third-party cloud
- Any cloud sync for vault or Logseq graph

## Consequences

- WireGuard setup is blocking for v2
- nixbox is the canonical data store — all other devices are clients
- The Raspberry Pi queues locally when away, syncs on return — no real-time cloud dependency
- GNOME desktop added to nixbox for Obsidian GUI access when monitor connected

## Open questions

- Obsidian sync to laptop when acquired — Syncthing is the planned approach
- Logseq graph access from T490s — SSHFS mount over SSH is the candidate
- Whether nixbox needs UPS protection given its role as primary data store
