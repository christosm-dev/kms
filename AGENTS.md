# AGENTS.md

## Purpose

This file is the working coordination and project-definition document for the Personal Knowledge Graph project. It is the active source of truth for current status, working principles, next steps, and open questions.

- `AGENTS.md` — this file, active coordination only
- `docs/adr/` — Architecture Decision Records, full decision context and status
- `docs/architecture/` — per-feature specs, reviewed before implementation

Update this file as product scope, architecture, and implementation details become clearer. Keep it focused on what is actively shaping current work.

---

## Current Product Direction

Build a self-hosted, privacy-first personal knowledge management system with three layers:

1. **Capture** — hands-free dictation and typed note entry, at home and portable
2. **Triage** — structured tagging against a personal taxonomy (domains, registers, valence, epistemic state, virtues)
3. **Agent** — local mentoring/life-coach agent reasoning over the enriched knowledge graph

All personal data stays on self-hosted infrastructure. No cloud services for vault content, Logseq graph, or agent inference.

---

## Agent Instructions

When working in this repository:

- Treat `AGENTS.md` as the top-level source of product and workflow context.
- Prefer updating this file when a requirement, assumption, or decision becomes concrete.
- Keep `docs/adr/README.md` updated when ADRs are added or their status changes.
- Keep `docs/architecture/README.md` updated when feature specs are added or completed.
- Record unresolved questions instead of inventing product behaviour silently.
- Keep changes additive and traceable — avoid deleting prior decisions unless clearly superseded.
- If implementation begins before a spec is complete, document the rationale for any assumptions made.
- Treat the Obsidian vault at `/home/cm/vault/` on nixbox as the canonical enriched data store.
- Treat the Logseq graph on nixbox as the canonical raw capture store — do not modify it programmatically.
- Never write secrets, bearer tokens, or API keys into committed files. Document variable names only.
- When a task needs secret-backed behaviour, prefer asking the user to confirm configuration exists or provide a sanitised value.
- Treat NixOS declarative configuration (`nix-config` repo) as the canonical environment definition — prefer adding packages and services there over ad hoc host installs.
- Do not assume nixbox is accessible — confirm SSH connectivity before running remote commands.
- If a pipeline run could modify vault content, confirm idempotency before executing.

---

## Feature Delivery Procedure

When starting or advancing a feature:

1. Create or update a feature spec at `docs/architecture/<feature-name>.md` before implementation begins.
2. Review the spec, surface open questions, gather real data before locking the design.
3. Update `AGENTS.md`, `docs/adr/README.md`, and relevant docs to record the feature and its status.
4. Implement, verify, and document the feature.
5. Update `AGENTS.md` decision log and feature progress on completion.
6. Prepare a concise commit message summary.

Feature branch and PR workflow applies if/when the project moves to a team or multi-device development model.

---

## Engineering Standards

- Prefer simple, elegant solutions over clever abstractions.
- Keep the codebase modular — small focused services with clear boundaries.
- Design for repeat runs and idempotent behaviour — pipelines must be safely re-runnable.
- Separate raw source data from normalised/enriched application data.
- Preserve original records — the vault note is a derived artefact, not the source of truth.
- Add abstractions only when they reduce complexity or duplication durably.
- Favour maintainability and readability over premature optimisation.
- Design for incremental growth — new capture sources and pipeline stages should not destabilise existing ones.
- Prefer testable seams around logic-heavy behaviour.

---

## Planning Principles

- **Source-first** — preserve original Logseq notes untouched; the vault is derived.
- **Separate raw from normalised** — raw Logseq blocks are not modified; vault notes are enriched derivatives.
- **Idempotent pipeline** — automated tagging runs must produce the same result on repeated execution; never blindly append.
- **Explicit merge rules** — conflicting metadata between Logseq and vault must follow documented rules (see ADR-005, ADR-006).
- **Overwrite protection** — `auto: false` notes are never touched by the pipeline (see ADR-003).
- **Time as a first-class dimension** — temporal pattern detection is a core agent capability, not an afterthought.
- **Privacy by default** — no personal data leaves self-hosted infrastructure; cloud sync options are explicitly rejected (see ADR-008).
- **Habit over tooling purity** — the capture habit matters more than architectural cleanliness; do not break working workflows for the sake of purity.

---

## Data Policy

- The vault and Logseq graph use live, real personal notes as their primary data source.
- Do not build pipeline behaviour or agent prompts around synthetic or mocked note content.
- If fixture data is needed for pipeline testing, keep it clearly scoped to test directories and explicitly labelled as synthetic.
- Any gap that forces reliance on mocked data should be documented as a temporary limitation to remove.

---

## Configuration And Secrets

- Bearer tokens and API keys go in NixOS secrets or environment variables — never in committed files.
- Non-secret configuration (vault path, taxonomy config, sync intervals) goes in YAML config files committed to the repo.
- Document required variable names and expected formats without committing real values.
- `LOGSEQ_ENABLED` config flag controls whether a FastAPI instance writes to the vault (nixbox) or queues only (VPS).

---

## Security Process

- VPS API is accessible only over WireGuard VPN — no public-facing port.
- Bearer token authentication on all API endpoints.
- Vault files are owned by `cm` user on nixbox — pipeline service runs as the same user.
- Do not log note content, bearer tokens, or personal metadata in service logs.
- A standing security process document should be created at `docs/security/security.md` before the FastAPI service goes live. (Deferred — not yet created.)

---

## Current Infrastructure

| Host | Device | Role | Status |
|---|---|---|---|
| `thinkpad25` | ThinkPad T490s WSL2 | Daily driver development | ✅ running |
| `nixbox` | NixOS server (192.168.0.24) | Vault, API, Obsidian, at-home capture | ✅ running |
| `contabo-vps` | Contabo VPS | Queue, public-facing services | ✅ running |
| `rpi` | Raspberry Pi 4 8GB | Portable away-from-home capture | ⏳ NixOS not yet installed |
| `laptop` | Linux laptop (future) | Primary Obsidian host | 🔮 not yet acquired |

nixbox Obsidian vault: `/home/cm/vault/` ✅ initialised
nixbox GNOME desktop: ✅ installed (monitor access for Obsidian)
nixbox Git repos: ⏳ not yet initialised

---

## Feature Progress

### Completed
- Triage tool v1 — self-contained HTML file with full taxonomy, wikilink conversion, auto state, virtue sliders, Obsidian export
- nixbox GNOME + Obsidian install — declarative NixOS configuration
- Obsidian vault initialisation at `/home/cm/vault/`
- ADR set — ADR-001 through ADR-009 covering all key architectural decisions

### In Progress
- Nothing currently active

### Planned (v1 MVP — in order)
1. **Git repos on nixbox** — bare `vault.git` and `logseq.git`, nightly auto-commit systemd timer
2. **FastAPI capture service on nixbox** — `POST /capture`, writes to `/home/cm/vault/daily/YYYY-MM-DD.md`
3. **Raspberry Pi NixOS install** — add as `rpi` host in `nix-config`, `aarch64-linux` target
4. **Triage tool on Pi browser** — served locally, keyboard-driven
5. **Logseq migration** — export and triage historical notes via `note-triage.html`
6. **WireGuard VPN** — nixbox ↔ VPS (prerequisite for v2)

### Planned (v2 — away from home)
7. VPS queue API — same FastAPI codebase, `LOGSEQ_ENABLED=false`
8. iOS Shortcut automation — Reminders → POST to nixbox or VPS
9. Graph visualisation server on nixbox (D3.js force graph)
10. LangFuse self-hosted on nixbox (Docker)
11. Local agent — Ollama + RAG + GraphRAG (deferred until Mac Mini or equivalent)

### Planned (v3 — iOS-free)
12. Mobile PWA — capture mode + VPS triage queue
13. Linux tablet (Surface Pro 5 ~$200 refurbished, GPD Pocket 4 as upgrade)
14. Flutter native app (iOS + Linux desktop) — when Linux laptop acquired
15. iOS components retired

---

## Decision Log

Lightweight table for decisions actively shaping current work. Full context in `docs/adr/`.

| Date | Decision | ADR | Notes |
|---|---|---|---|
| 2026-05-06 | Obsidian vault as enriched store, Logseq retained as capture and browsing interface | ADR-001 | Logseq is first-class, not deprecated |
| 2026-05-06 | Four-dimension taxonomy + virtues in Greek | ADR-002 | Virtues rated 0-10, 5 = good enough |
| 2026-05-06 | Three-state auto field: pending / true / false | ADR-003 | auto:false is permanent overwrite lock |
| 2026-05-06 | Tiered capture: nixbox at home, Pi portable, Siri fallback | ADR-004 | Pi is not always-on, powers off when not travelling |
| 2026-05-06 | Taxonomy-aware wikilink conversion | ADR-005 | Taxonomy terms → tags, all others → preserved [[links]] |
| 2026-05-06 | 1:1 Logseq → vault mapping for v1, logseq_id deferred | ADR-006 | No splitting until reconciliation logic exists |
| 2026-05-06 | GraphRAG agent + LangFuse observability | ADR-007 | Deferred until inference hardware acquired |
| 2026-05-06 | Self-hosted only, no cloud sync | ADR-008 | iCloud and Dropbox explicitly rejected |
| 2026-05-06 | HTML → PWA → Flutter platform strategy | ADR-009 | PWA for v2, Flutter for v3 |
| 2026-05-06 | GNOME + Obsidian on nixbox, monitor when needed | — | nixbox has HDMI, not permanently headless |
| 2026-05-06 | Bare Git repos over SSH for vault and Logseq versioning | — | Gitea deferred to v2 |
| 2026-05-06 | Spec-first feature workflow adopted from watch-history project | — | docs/architecture/ for feature specs before implementation |
| 2026-05-06 | AGENTS.md pattern adopted from watch-history project | — | Active coordination file, decisions archived separately |

---

## Open Questions

- **Shokz button interception** — AVRCP via D-Bus on nixbox untested; BTHome Shelly BLU (~£15) is fallback
- **Bluetooth dongle HFP** — Edimax on nixbox untested for HFP mic input; CSR chipset dongle as fallback
- **Headset selection** — Shokz (current, zero cost) vs Plantronics Voyager Legend 50 UC (cleaner Linux HFP, USB dongle, ~£40 refurbished)
- **Obsidian sync to laptop** — Syncthing planned when Linux laptop acquired
- **Logseq access from T490s** — SSHFS over SSH is the candidate; no decision made
- **Whisper model size** — tiny vs base trade-off on nixbox (speed vs accuracy); untested
- **Automated pipeline LLM** — Ollama for auto-tagging deferred until agent hardware acquired
- **Logseq as primary browsing interface** — relationship between Logseq graph and Obsidian vault still forming; requirements emerging through use
- **docs/security/security.md** — not yet created; needed before FastAPI service goes live
- **Pi Bluetooth** — whether Pi HFP setup is sufficiently simpler than nixbox for portable dictation

---

## Next Discovery Steps

1. Test Shokz AVRCP button events via D-Bus on nixbox — determines whether additional button hardware is needed
2. Test Edimax dongle HFP mic input with Shokz on nixbox under PipeWire
3. Initialise bare Git repos on nixbox (`vault.git`, `logseq.git`)
4. Spec the FastAPI capture service at `docs/architecture/fastapi-capture-service.md`
5. Begin Pi NixOS install
