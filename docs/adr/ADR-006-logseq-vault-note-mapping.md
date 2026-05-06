# ADR-006: Logseq to Vault Note Mapping

**Date:** 2026-05-06
**Status:** Proposed
**Supersedes:** —

## Context

The automated ingestion pipeline converts Logseq notes into Obsidian vault notes. A Logseq page can contain multiple distinct thoughts as child blocks, which raises the question of whether to split them into individual vault notes or maintain a 1:1 relationship.

Splitting would produce richer, more granular vault notes but introduces a reconciliation problem: when a source Logseq note is later manually triaged, the pipeline must know which vault notes were generated from it and how to handle conflicts between auto-tagged splits and a manually triaged replacement.

## Decision

**v1: Strict 1:1 relationship between Logseq notes and vault notes.**

Each Logseq block/page maps to exactly one vault note. No splitting.

**If splitting is introduced in a future version:**
- A `logseq_id` provenance field must be added to all auto-generated vault note frontmatter
- The reconciliation strategy is: merge manually triaged tags across all splits, never overwrite `auto: false` notes
- The pipeline checks `logseq_id` to identify related vault notes before processing

```yaml
# Example provenance field (future use only)
logseq_id: 64a3f2b1-8c4d-4e2a-9f1b-3d7e6a5c2b8f
logseq_block: "Keep postponing the difficult conversation..."
```

## Consequences

- v1 pipeline is simpler — no reconciliation logic needed
- 1:1 mapping means some vault notes may be longer than ideal for RAG chunking
- Chunking strategy for RAG can compensate by splitting at the embedding stage without affecting vault file structure
- The `logseq_id` design is documented now so it can be implemented cleanly when needed

## Open questions

- Optimal chunking strategy for RAG when vault notes are long (sentence-level, paragraph-level, or fixed-token)
- Whether Logseq block UUIDs are stable enough to serve as reliable provenance keys
