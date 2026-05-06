# Personal Knowledge Graph

A self-hosted, privacy-first personal knowledge management system with three layers:

1. **Capture** — hands-free dictation and typed note entry, at home and portable
2. **Triage** — structured tagging against a personal taxonomy
3. **Agent** — local mentoring/life-coach agent reasoning over the enriched knowledge graph

All personal data stays on self-hosted infrastructure. No cloud services.

---

## Quick orientation

| File / Directory | Purpose |
|---|---|
| `AGENTS.md` | Active coordination — current status, workflow, open questions, next steps |
| `docs/adr/` | Architecture Decision Records — decisions and feature specs |
| `taxonomy.yaml` | SDD source of truth for the note taxonomy *(to be created)* |
| `openapi.yaml` | SDD source of truth for the FastAPI API contract *(to be created)* |
| `src/note-triage.html` | Self-contained desktop triage tool — load in any browser |
| `docs/security/security.md` | Standing security process guide *(to be created)* |

Start with `AGENTS.md` for current status and next steps. Start with `docs/adr/README.md` for architectural decisions.

---

## Current structure

### Triage tool (`note-triage.html`)
Self-contained single HTML file. No build step, no server required. Loads a Logseq Markdown export, presents one note at a time for structured tagging, and exports Obsidian-ready `.md` files locally. Also exports skipped notes as a review file. Run in any browser — nothing leaves the machine.

### Architecture Decision Records (`docs/adr/`)
ADRs serve as both decision records and feature specs. A `Proposed` ADR is the active spec for a feature under design. An `Accepted` ADR is a decision in effect with a corresponding GitHub Epic and Issues. See `docs/adr/README.md` for the full index.

### SDD specs (`taxonomy.yaml`, `openapi.yaml`)
Specification Driven Development is applied selectively to the two components where a formal spec genuinely generates value:
- `taxonomy.yaml` — generates triage tool tag pickers, pipeline vocabulary, and vault frontmatter schema
- `openapi.yaml` — generates FastAPI endpoint stubs and API client structure

Both are to be created as the first implementation work begins.

---
