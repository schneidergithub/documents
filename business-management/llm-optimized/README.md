---
doc_id: README
title: "SOFDEV 310 Product Strategy — LLM Knowledge Pack"
course: "UW SOFDEV 310 Product Strategy"
last_reviewed: "2026-02-10"
sources:
  - Course PDFs (Sessions 1–3)
  - Modern addendum sources (see meta/sources.md)
tags:
  - index
  - llm
  - product-strategy
---
## Purpose

A folder-and-file split of course topics optimized for:
- Retrieval Augmented Generation (RAG)
- Tool calling / structured extraction
- Deterministic “what-to-read-next” navigation

## Design principles (LLM-friendly)
- Small, single-topic files.
- Stable headings, explicit inputs/outputs, and “definitions first”.
- Course-derived content is separated from the Modern Addendum.
- Diagrams are provided in Mermaid (text-first, LLM parseable).

## Quick start
1. Start with `course/overview.md`.
2. Use `meta/glossary.md` for consistent definitions.
3. For modern “gold standard” alignment, read `modern_addendum/alignment_overview.md`.

## Contents (high-level)
- `course/` — session-derived content (primary source)
- `modern_addendum/` — widely used contemporary product practice (secondary source)
- `diagrams/` — Mermaid diagrams
- `ontology/` — a minimal JSON ontology for tool/agent use
- `meta/` — sources, glossary, chunking guide, and manifest
