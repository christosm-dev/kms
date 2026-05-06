# Architecture Specs

Feature specs live here. Each spec is created and reviewed **before implementation begins**.

Specs capture intent, open questions, and design decisions for a specific feature. Once implemented, the spec is marked delivered with a date. Full architectural decisions are in `docs/adr/`.

## Index

| Spec | Feature | Status |
|---|---|---|
| *(none yet)* | — | — |

## How to use this directory

1. Create `<feature-name>.md` here before writing any code
2. Review the spec — surface open questions, gather real data
3. Update `AGENTS.md` feature progress when implementation starts
4. Mark as `Delivered: YYYY-MM-DD` here when the feature is complete

## Planned specs (not yet written)

- `fastapi-capture-service.md` — POST /capture endpoint, daily note append, vault write logic
- `whisper-transcription-pipeline.md` — audio capture trigger, HFP activation, Whisper model, nixbox vs Pi
- `automated-ingest-pipeline.md` — Logseq export conversion, taxonomy-aware wikilink handling, auto-tagging
- `git-versioning-setup.md` — bare repos on nixbox, nightly systemd timer, gitignore conventions
- `pwa-triage-app.md` — PWA manifest, service worker, VPS queue integration (v2)
- `graphrag-agent.md` — RAG + graph traversal, LangFuse observability, Ollama stack (deferred)
