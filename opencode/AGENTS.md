# AGENTS.md

## Orchestrator Architecture Context

This repository contains a Node.js / TypeScript orchestration pipeline for deterministic migration of legacy stored procedures into Java services using a staged artifact-driven workflow.

The pipeline evolves incrementally and must remain auditable at every step.

---

## Pipeline Stages

Current migration stages:

* init
* numbering
* diagnosis
* traceability
* design
* codegen
* review

Current maturity:

init → stable
numbering → stable MVP
diagnosis → stable MVP
traceability → extraction evolving
design → active development
codegen → planned
review → planned

---

## Architectural Model

The orchestrator is:

artifact-driven
stage-oriented
deterministic
incrementally extensible

Artifacts are the source of truth, not conversations.

---

## Source-of-Truth Hierarchy

Line references → numbered stored procedure
Semantics → diagnosis.md
Structure → traceability-map.json
Architecture → design.md

---

## Core Rules

Never invent business rules.

Never silently simplify legacy behavior.

Never promote LOW-confidence rules to Domain Service.

Never redesign the entire pipeline when only one stage is requested.

Prefer explicit contracts over implicit inference.

Prefer incremental safe evolution over architectural jumps.

Prefer semantic chunking over prompt compression.

---

## Diagnosis Principles

Diagnosis is analysis, not architecture.

Diagnosis must preserve ambiguity when ambiguity exists.

Unknown dependencies must remain explicit.

Diagnosis must remain auditable and structured.

---

## Traceability Principles

Traceability maps diagnosis semantics into structured migration blocks.

Traceability preserves:

line ranges
dependencies
confidence levels
transaction signals

Traceability may suggest architectural routing but must NOT finalize architecture.

---

## Design Principles

Design consumes:

diagnosis.md
traceability-map.json
numbered stored procedure

Design must:

identify reuse opportunities
identify extension opportunities
identify new artifacts explicitly

Design must model transaction ownership explicitly.

---

## Code Generation Direction

Codegen must become reuse-aware.

Prefer:

reuse
extension
adapter wrapping

over duplication.

Every new artifact must be justified.

---

## Model Interaction Rules

When assisting this repository:

diagnose before proposing
explain root cause before changing architecture
propose only the next safe incremental step

Do NOT:

perform speculative redesign
collapse stages
invent missing semantics
optimize prematurely

Always preserve pipeline determinism.
