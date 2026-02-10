---
doc_id: DIAGRAM_TALC
title: "Diagram: Technology Adoption Life Cycle (TALC)"
course: "UW SOFDEV 310 Product Strategy"
last_reviewed: "2026-02-10"
sources:
  - Session 1/2 (TALC slide)
tags:
  - diagram
  - mermaid
  - course
---
```mermaid
stateDiagram-v2
  [*] --> Innovators
  Innovators --> Early_Adopters
  Early_Adopters --> Chasm
  Chasm --> Early_Majority
  Early_Majority --> Late_Majority
  Late_Majority --> Laggards
  Laggards --> [*]
```
