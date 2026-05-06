# ADR-005: Wikilink Conversion Strategy

**Date:** 2026-05-06
**Status:** Proposed
**Supersedes:** —

## Context

Logseq uses `[[wikilink]]` syntax for two distinct purposes:
1. **Conceptual links** — genuine relationships between ideas (e.g. `[[decision-making]]`, `[[stoicism]]`, `[[Naval Ravikant]]`)
2. **Tags masquerading as links** — categorisation using Logseq's link syntax (e.g. `[[health]]`, `[[work]]`)

The original triage tool converted all `[[wikilinks]]` to `#tags`, which was found to be too aggressive — it destroys the graph topology encoded in conceptual links that the agent needs for graph-aware traversal (GraphRAG pattern).

The agent architecture requires both:
- **Tags** for structured taxonomy signal (RAG retrieval)
- **`[[links]]`** for graph topology (graph traversal)

## Decision

Implement a taxonomy-aware wikilink conversion strategy:

**Rule:** If a wikilink matches a known taxonomy term → convert to `#tag`. Otherwise → preserve as Obsidian `[[link]]`.

```python
TAXONOMY_TAGS = {
    # domains
    'work', 'finances', 'health', 'relationships', 'learning',
    'creativity', 'purpose', 'environment',
    'professional-development', 'personal-development',
    # registers  
    'insight', 'pattern', 'tension', 'decision', 'gratitude',
    'obstacle', 'intention', 'question', 'ambiguity',
    'idea', 'eureka', 'interesting', 'follow-up',
    # valence
    'energising', 'draining', 'calming', 'neutral',
    # epistemic
    'certain', 'validating', 'doubtful', 'sceptical',
    'indifferent', 'encouraging', 'accommodating',
    'rejuvenating', 'confident'
}

def convert_wikilink(link_text):
    normalised = link_text.lower().replace(' ', '-')
    if normalised in TAXONOMY_TAGS:
        return f'#{normalised}'         # → flat tag
    else:
        return f'[[{link_text}]]'       # → preserved Obsidian link
```

**Examples:**
- `[[health]]` → `#health` (taxonomy term)
- `[[decision-making]]` → `[[decision-making]]` (concept page, preserved)
- `[[stoicism]]` → `[[stoicism]]` (concept page, preserved)
- `[[Naval Ravikant]]` → `[[Naval Ravikant]]` (person page, preserved)

This is implemented in the triage tool's `convertWikilinks()` function and in the automated ingestion pipeline.

## Consequences

- The vault develops genuine graph topology over time — preserved links become traversable edges
- The agent can perform GraphRAG: RAG retrieval for seed notes + graph traversal for neighbourhood context
- Obsidian's graph view reflects meaningful conceptual relationships, not just tag categories
- The taxonomy vocabulary must be kept in sync between the triage tool and the pipeline
- Notes with many preserved `[[links]]` are higher-value graph nodes

## Open questions

- Whether to surface unrecognised wikilinks in the triage UI for manual classification (keep as link, convert to tag, or discard)
- Whether to maintain a separate vocabulary file for taxonomy terms (single source of truth for triage tool + pipeline)
- 1:1 Logseq → vault relationship maintained for v1 — splitting deferred. If splitting is introduced, a `logseq_id` provenance field is required for reconciliation (see ADR-006)
