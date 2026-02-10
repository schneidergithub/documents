---
doc_id: META_CHUNKING
title: "Chunking guide for RAG and LLM ingestion"
course: "UW SOFDEV 310 Product Strategy"
last_reviewed: "2026-02-10"
sources:
  - Course PDFs
  - This pack structure
tags:
  - meta
  - rag
  - chunking
---
## Recommended ingestion strategy

- Index each file as one “document unit”.
- Chunk within a file by H2 sections (`##`) when the file exceeds ~1,000–1,500 tokens.
- Preserve YAML front matter as metadata (doc_id, tags, sources).

## Retrieval hints
- Prefer `meta/glossary.md` for terms: Market, Segment, Positioning, etc.
- Prefer `course/processes/*` for “how to” workflows.
- Prefer `modern_addendum/*` for contemporary process patterns (discovery cadence, OST, NSM, prioritization).

## Suggested metadata fields (if your RAG stack supports them)
- `doc_id`
- `title`
- `tags`
- `source_scope`: `course` or `modern_addendum`
- `session`: 1 | 2 | 3 (course files)
