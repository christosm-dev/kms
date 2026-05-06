# AGENTS.md

## Purpose

This file is the working coordination and project-definition document for the Personal Knowledge Graph project. It is the active source of truth for current status, working principles, next steps, and open questions.

- `AGENTS.md` — this file, active coordination only
- `docs/adr/` — Architecture Decision Records, full decision context and status; `Proposed` ADRs serve as feature specs
- `taxonomy.yaml` — SDD source of truth for the note taxonomy (generates triage tool, pipeline vocabulary, schema)
- `openapi.yaml` — SDD source of truth for the FastAPI API contract (generates stubs and client POST structure)

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
- Record unresolved questions instead of inventing product behaviour silently.
- Keep changes additive and traceable — avoid deleting prior decisions unless clearly superseded.
- If implementation begins before an ADR is accepted, document the rationale for any assumptions made.
- Treat the Obsidian vault at `/home/cm/vault/` on nixbox as the canonical enriched data store.
- Treat the Logseq graph on nixbox as the canonical raw capture store — do not modify it programmatically.
- Treat `taxonomy.yaml` as the single source of truth for the note taxonomy — do not hardcode taxonomy terms elsewhere.
- Treat `openapi.yaml` as the single source of truth for the API contract — do not define endpoints outside of it.
- Never write secrets, bearer tokens, or API keys into committed files. Document variable names only.
- When a task needs secret-backed behaviour, prefer asking the user to confirm configuration exists or provide a sanitised value.
- Treat NixOS declarative configuration (`nix-config` repo) as the canonical environment definition — prefer adding packages and services there over ad hoc host installs.
- If a pipeline run could modify vault content, confirm idempotency before executing.
- When an ADR moves to Accepted, check whether a GitHub Epic and Issues should be created before implementation begins.
- Reference GitHub Issue numbers in commit messages and AGENTS.md feature progress entries.

---

## Feature Delivery Procedure

### Full workflow — decision to merged code

```
1. Decision emerges from conversation or experimentation
2. ADR written in docs/adr/ with status: Proposed
   - Proposed ADR IS the feature spec — no separate spec file needed
   - Captures context, design intent, open questions, consequences
3. AGENTS.md decision log updated with one-line entry linking to ADR
4. ADR reviewed — open questions resolved, design settled
5. ADR status → Accepted
6. SDD specs written if applicable (see SDD Policy below)
7. GitHub Epic created — one Epic per ADR or coherent feature scope
8. GitHub Issues created under the Epic — discrete implementable tasks
9. AGENTS.md feature progress updated with Epic/Issue references
10. Feature branch per issue — named from issue title (kebab-case)
11. Implementation on branch — commit messages reference issue number
12. PR raised — title from issue/branch name, short outcome-focused description
13. PR merged → issue closed automatically
14. ADR status confirmed Accepted if not already
15. AGENTS.md feature progress marked complete
```

### Branch and PR conventions

- Branch name: `feature/<issue-title-kebab>` or `fix/<issue-title-kebab>`
- Commit messages: `type: description (#issue-number)` e.g. `feat: add POST /capture endpoint (#3)`
- PR title: Title Case from branch name with hyphens replaced by spaces
- PR description: short, outcome-focused — what the change achieves, not implementation detail

### When to skip steps

- Skip GitHub Epic/Issues for trivial changes (config fixes, doc updates) — commit directly to main
- Skip branch for single-file documentation changes
- Never skip the ADR for decisions that affect architecture, data schema, or pipeline behaviour

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

## SDD Policy (Specification Driven Development)

SDD is applied selectively — only where a formal spec genuinely generates value by being the single source of truth that multiple artefacts are derived from.

### Apply SDD strictly to

**`taxonomy.yaml` — note taxonomy**
The single source of truth for all taxonomy terms (domains, registers, valence, epistemic state, virtues). All of the following are derived from it and must not define taxonomy terms independently:
- Triage tool `TAXONOMY` constant and `TAXONOMY_TAGS` set
- Automated pipeline classification vocabulary
- Vault frontmatter schema
- ADR-002 taxonomy tables

**`openapi.yaml` — API contract**
The single source of truth for the FastAPI capture service API. All of the following are derived from it:
- FastAPI endpoint stubs
- iOS Shortcut POST body structure
- VPS queue API endpoints
- API documentation

### Do not apply SDD to

- Architecture decisions — use ADRs
- Infrastructure configuration — use NixOS declarative config
- Agent pipeline reasoning — not formally specifiable
- Knowledge graph structure — emergent, not specifiable upfront
- Taxonomy evolution — ADR-002 tracks changes, taxonomy.yaml is updated accordingly

### SDD change process

When taxonomy.yaml or openapi.yaml changes:
1. Update the spec file
2. Regenerate or update all derived artefacts in the same commit
3. Note the change in AGENTS.md decision log if it reflects a meaningful taxonomy decision
4. Never update a derived artefact without updating the spec first

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
| 2026-05-06 | AGENTS.md pattern adopted from watch-history project | — | Active coordination file, ADRs for full decision context |
| 2026-05-06 | Proposed ADR serves as feature spec — no separate spec files | — | Eliminates docs/architecture/ folder duplication |
| 2026-05-06 | Selective SDD: taxonomy.yaml and openapi.yaml as generative specs | — | All other decisions use ADRs |
| 2026-05-06 | ADR → GitHub Epic → Issues → branch → PR workflow | — | Explicit mapping from decision to implementation |

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
4. Write ADR-010 for FastAPI capture service (Proposed) — this IS the spec
5. Begin Pi NixOS install
