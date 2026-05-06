# ADR-009: Triage Tool Platform Strategy

**Date:** 2026-05-06
**Status:** Accepted
**Supersedes:** —

## Context

The triage tool needs to run on multiple platforms — desktop browser for the Logseq migration, mobile for on-the-go tagging, and eventually as a native app. Three approaches were evaluated: single-file HTML, PWA, and Flutter.

## Decision

**Three-phase platform strategy:**

**v1 — Self-contained HTML file (`note-triage.html`)**
- Single file, no build step, runs in any browser
- Loads Logseq export, presents notes for manual tagging
- Exports tagged notes as Obsidian-ready `.md` files locally
- Exports skipped notes as `skipped-notes-YYYY-MM-DD.md`
- No server required — privacy preserved, nothing leaves the browser

**v2 — Progressive Web App (PWA)**
- Hosted on VPS behind HTTPS (Let's Encrypt)
- Installable on iOS and Android via "Add to Home Screen"
- Two modes: new note capture + triage queue from VPS
- Offline-capable via service worker (notes queued locally, synced on connectivity)
- Single codebase from the existing HTML triage tool — manifest.json + service worker added
- Full taxonomy tagging on mobile — no deferred tagging

**v3 — Flutter (native iOS + Linux desktop)**
- Single Dart codebase for iOS and Linux GNOME
- Native performance, no browser required
- Triggered when Linux laptop is acquired
- PWA remains as fallback

**Migration path:** v1 HTML → v2 PWA is a promotion of the existing codebase. v2 → v3 Flutter rewrites the UI in Dart but reuses the same API and taxonomy.

## Consequences

- No React or other framework needed for v1/v2 — vanilla HTML/JS sufficient
- The taxonomy is defined once in the HTML file and must be kept in sync with the pipeline's TAXONOMY_TAGS (ADR-005)
- Full tagging at capture time on mobile is preferred over deferred tagging — reduces the auto:pending backlog
- Flutter on NixOS requires community flake (`flutter-nix`) — not in nixpkgs mainline

## Open questions

- Whether the PWA needs a separate backend or reuses the existing FastAPI capture service
- Service worker strategy for offline note queuing (IndexedDB vs in-memory)
- Whether Flutter is still the right v3 choice when the time comes
