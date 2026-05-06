# ADR-003: Note Lifecycle and Auto-Tagging States

**Date:** 2026-05-06
**Status:** Proposed
**Supersedes:** —

## Context

Notes enter the vault via multiple paths — manual triage, automated pipeline, and direct capture. The agent needs to know the provenance and confidence level of each note's metadata. A mechanism is needed to prevent automated processing from overwriting manually enriched notes, while still allowing progressive enrichment over time.

## Decision

A three-state `auto` field in every note's YAML frontmatter:

```yaml
auto: pending   # captured but not yet processed
auto: true      # tags assigned by automated pipeline
auto: false     # manually triaged via triage tool — never overwritten
```

**State transitions:**
```
captured → auto: pending  (set automatically at capture time)
pending  → auto: true     (automated pipeline processed it)
pending  → auto: false    (manually triaged directly)
true     → auto: false    (manually triaged after pipeline ran)
```

**Overwrite protection:**
- The automated pipeline NEVER overwrites `auto: false` notes
- `auto: true` notes may be reprocessed by the pipeline on re-runs
- `auto: false` is a permanent lock — only the human can change it

**The triage tool sets:**
- `auto: false` on Save
- `auto: pending` on Skip (returns to queue)
- No file created on Discard

## Consequences

- Every note has a clear provenance trail
- The agent can weight `auto: false` notes more heavily for synthesis
- Skipped notes land in `skipped-notes-YYYY-MM-DD.md` for later review
- The automated pipeline needs to check `auto` field before processing
- Dataview queries can filter by `auto` state: `WHERE auto = "false"`

## Open questions

- Whether `auto: true` notes should be periodically re-evaluated as the pipeline improves
- Whether a confidence score should accompany `auto: true` tags
