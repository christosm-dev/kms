# Architecture Decision Records

This directory contains ADRs for the Personal Knowledge Graph project.

ADRs serve two purposes:
- **Accepted** ADRs — decisions in effect, shaping the codebase
- **Proposed** ADRs — design intent under discussion, serving as the feature spec before implementation begins

There are no separate feature spec files. A `Proposed` ADR IS the spec. When the design is settled and implementation is ready to begin, the ADR moves to `Accepted` and a GitHub Epic + Issues are created.

Check the status field before relying on any decision. See `AGENTS.md` for the full workflow.

## Index

| ADR | Title | Status | GitHub Epic |
|---|---|---|---|
| [ADR-001](ADR-001-knowledge-store-selection.md) | Knowledge Store Selection | Accepted | — |
| [ADR-002](ADR-002-note-taxonomy-design.md) | Note Taxonomy Design | Accepted | — |
| [ADR-003](ADR-003-note-lifecycle-auto-states.md) | Note Lifecycle and Auto-Tagging States | Accepted | — |
| [ADR-004](ADR-004-capture-architecture.md) | Capture Architecture | Accepted | — |
| [ADR-005](ADR-005-wikilink-conversion-strategy.md) | Wikilink Conversion Strategy | Proposed | — |
| [ADR-006](ADR-006-logseq-vault-note-mapping.md) | Logseq to Vault Note Mapping | Accepted | — |
| [ADR-007](ADR-007-agent-architecture-observability.md) | Agent Architecture and Inference Observability | Proposed | — |
| [ADR-008](ADR-008-infrastructure-hosting.md) | Infrastructure and Hosting | Accepted | — |
| [ADR-009](ADR-009-triage-tool-platform-strategy.md) | Triage Tool Platform Strategy | Accepted | — |

## Status definitions

- **Proposed** — design under discussion; this ADR is the active feature spec; implementation has not begun
- **Accepted** — decision is in effect; GitHub Epic, Issues or SDDs are being / have been defined
- **Deferred** — acknowledged but not yet decided; no implementation work
- **Superseded** — replaced by a newer ADR (link provided in the ADR itself)
