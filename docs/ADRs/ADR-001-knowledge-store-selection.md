# ADR-001: Knowledge Store Selection

**Date:** 2026-05-06
**Status:** Accepted
**Supersedes:** —

## Context

The project requires a personal knowledge store that can serve three purposes simultaneously:
- A human-readable note browsing interface
- A structured input for an AI agent pipeline
- A graph-navigable knowledge structure with bidirectional links

Logseq was the existing tool in use. Obsidian was evaluated as an alternative. Plain Markdown was identified as the ideal agent input format.

## Decision

Use **Obsidian** as the primary vault format for the enriched knowledge store, with **Logseq** retained as a first-class browsing and fast-capture interface.

- Obsidian vault lives on nixbox at `/home/cm/vault/`
- Logseq graph lives on nixbox, accessed from Windows via native app
- The two stores coexist — Logseq for fast capture and browsing, Obsidian vault for enriched, agent-ready notes
- Migration from Logseq to Obsidian vault is handled via the triage tool

## Consequences

- Plain Markdown with YAML frontmatter is the canonical note format in the vault
- Logseq's outliner format is not required to be abandoned — it remains valid for capture
- A conversion/triage pipeline is needed to move notes from Logseq into the vault
- Obsidian's graph view and Dataview plugin provide visualisation without custom tooling
- The agent pipeline reads from the Obsidian vault, not directly from Logseq

## Open questions

- Sync strategy between Logseq graph and Obsidian vault (SSHFS, Syncthing, or manual export)
- Whether Logseq eventually becomes read-only archive or continues as active capture tool
- Long-term: Obsidian migrates to Linux laptop when one is acquired
