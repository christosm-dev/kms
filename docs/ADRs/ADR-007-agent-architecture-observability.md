# ADR-007: Agent Architecture and Inference Observability

**Date:** 2026-05-06
**Status:** Proposed
**Supersedes:** —

## Context

The long-term goal of this project is a local mentoring/life-coach agent that reasons over the personal knowledge vault. The agent needs to be trustworthy — meaning every response must be traceable back to the vault notes that grounded it. Without observability, the agent is a black box and its coaching output cannot be trusted or improved.

Hardware constraint: a capable local inference device (Mac Mini M4 Pro 64GB or equivalent) is not yet available. The agent layer is deferred until hardware is acquired.

## Decision

**Agent architecture: GraphRAG**

Combining vector similarity search with graph traversal for richer context retrieval:

```
Query
  ↓
Phase 1 — RAG: ChromaDB vector search → seed notes
  ↓
Phase 2 — Graph traversal: follow [[links]] from seed notes → neighbourhood
  ↓
Phase 3 — Re-rank: score by relevance + connection strength + auto:false weight
  ↓
Phase 4 — Synthesise: Ollama LLM reasons over combined context
  ↓
Response + source attribution
```

**Stack (all local on nixbox/Mac Mini):**
- Embeddings: Ollama + `nomic-embed-text`
- Vector store: ChromaDB or pgvector
- LLM: Ollama + Mistral or Llama 3 (model TBD based on hardware)
- Orchestration: LangChain or LlamaIndex

**Inference observability: LangFuse (self-hosted on nixbox)**
- Runs as Docker container alongside existing nixbox Docker setup
- Full trace per query: retrieval steps, similarity scores, context assembly, model response, latency
- Source attribution — every response linked to specific vault notes
- Query audit log — full history of prompts and context used
- Privacy preserved — no data leaves nixbox

**Instrumentation is a first-class requirement** — the agent pipeline is instrumented with LangFuse from day one, not retrofitted.

## Consequences

- The vault's `[[link]]` preservation (ADR-005) is essential for Phase 2 graph traversal
- The `auto: false` flag (ADR-003) enables higher weighting of manually triaged notes in Phase 3
- The taxonomy tags (ADR-002) enable domain-scoped traversal in Phase 2
- LangFuse requires Docker on nixbox — already available
- Agent layer deferred until Mac Mini or equivalent hardware acquired
- Periodic synthesis ("patterns from the last month") is preferred over pure Q&A

## Open questions

- LLM model selection — depends on hardware acquired
- Whether to use LangChain or LlamaIndex for orchestration
- Evaluation framework (RAGAS or DeepEval) for measuring agent response quality
- Whether the agent exposes an MCP server endpoint for conversational access (`get_recent_notes()`)
