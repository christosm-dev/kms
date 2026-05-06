# ADR-002: Note Taxonomy Design

**Date:** 2026-05-06
**Status:** Proposed
**Supersedes:** —

## Context

The agent pipeline requires structured metadata on each note to enable meaningful retrieval, pattern detection, and synthesis. A flat tagging approach was considered but found insufficient for the mentoring/life-coach use case, which requires multiple orthogonal dimensions of classification.

The taxonomy needs to balance richness (agent signal quality) against friction (capture and triage effort).

## Decision

A four-dimensional taxonomy stored in YAML frontmatter:

**Life domains** (multi-select)
`creativity` `environment` `finances` `health` `learning` `personal-development` `professional-development` `purpose` `relationships` `work`

**Reflective register** (multi-select)
`ambiguity` `decision` `eureka` `follow-up` `gratitude` `idea` `insight` `intention` `interesting` `obstacle` `pattern` `question` `tension`

**Energy valence** (single-select)
`draining` → `neutral` → `calming` `energising`

**Epistemic state** (single-select)
`doubtful` `sceptical` → `indifferent` → `neutral` → `validating` `encouraging` → `accommodating` `rejuvenating` `confident`

**Virtues/values** (optional, 0–10 slider, 5 = good enough)
Stored in Greek with English translation:
- Αναζωογονητικός (rejuvenating) — others-first reminder, listed first
- Ήρεμος (calm)
- Υπομονετικός (patient)
- Συγκεντρωμένος (focused)
- Εκλεκτικός (discerning)
- Δραστήριος (active)

Greek etymology is significant — these are displayed in Greek in the UI.

## Consequences

- The triage tool enforces this taxonomy via constrained tag pickers
- Not all dimensions are required — domain is the minimum at triage time
- The taxonomy vocabulary is used to distinguish wikilinks from tags (see ADR-005)
- Virtue ratings appear as dedicated frontmatter fields: `ήρεμος (calm): 7/10`
- The agent can weight manually triaged notes higher than auto-tagged ones

## Open questions

- Whether the taxonomy needs versioning as it evolves
- Whether additional virtues/values will be added over time
- Whether register and epistemic state should be multi-select in future
