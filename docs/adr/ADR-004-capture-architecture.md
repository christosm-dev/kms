# ADR-004: Capture Architecture

**Date:** 2026-05-06
**Status:** Proposed 
**Supersedes:** —

## Context

Notes need to be captured in multiple contexts — at home typed, at home via dictation, away from home. The system must minimise friction at capture time while maintaining privacy (no cloud dependencies) and supporting the eventual agent pipeline.

Multiple hardware and software paths were evaluated including ESP32, Pi Zero, iOS Shortcuts, PWA, and dedicated Linux devices.

## Decision

A tiered capture architecture with a clear MVP scope:

**v1 — At home (MVP)**
- **Typed:** SSH from T490s/laptop → nixbox → triage tool in browser
- **Dictation:** Shokz/mono headset + button → nixbox → Whisper → vault
- **Away fallback:** Siri → iOS Reminders → manual transcription on return
- No VPS involvement, no iOS automation, no PWA

**v2 — Away from home**
- Raspberry Pi 4 8GB in backpack on battery pack (~20,000mAh)
- SSH from laptop → Pi triage tool
- Pi queues notes locally, syncs to nixbox on return
- VPS queue API for async routing when Pi not available
- iOS Shortcut automation as secondary path

**v3 — Fully iOS-free**
- Linux tablet (Surface Pro 5 refurbished ~$200 as entry point, GPD Pocket 4 as upgrade)
- Full NixOS stack, Sway WM
- iOS components retired

**Capture device roles:**
- nixbox — primary at-home capture node, vault storage, Whisper transcription
- Raspberry Pi — portable away-from-home node only, not always-on
- T490s WSL2 — daily driver, SSH access to nixbox

## Consequences

- iOS remains in the loop for v1/v2 as a fallback only
- WireGuard VPN is a prerequisite for v2 away-from-home routing
- The Pi is powered off when not travelling — not an always-on home server
- nixbox Bluetooth dongle (currently Edimax, upgrade to CSR if HFP issues) handles at-home dictation
- Button trigger path: Shokz AVRCP via D-Bus (test first), BTHome Shelly BLU as fallback (~£15)

## Open questions

- Shokz AVRCP button event support on Linux — test pending
- Whether Plantronics Voyager Legend 50 / Voyager 5200 UC is preferable to Shokz for dictation (better mic, cleaner Linux HFP, USB dongle sidesteps profile switching)
- Whisper model size on nixbox vs Pi (tiny/base trade-off: speed vs accuracy)
- Pi NixOS install — `aarch64-linux` target, differs from existing `x86_64-linux` hosts
