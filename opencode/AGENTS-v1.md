# AGENTS.md

## Orchestrator Architecture Context

This repository contains a Node.js/TypeScript orchestration pipeline for legacy stored procedure migration.

The pipeline is artifact-driven and stage-based.

## Primary Objective

Evolve a deterministic migration orchestrator that transforms a legacy stored procedure into a Java migration workflow through explicit stages and persisted artifacts.

## Pipeline Stages

* init
* numbering
* diagnosis
* traceability
* design
* codegen
* review

## Current State

* init: operational
* numbering: operational MVP
* diagnosis: MVP functional
* traceability: contract defined, extraction logic evolving
* design: under construction
* codegen: planned
* review: planned

## Core Architectural Principles

* The pipeline is artifact-driven, not conversation-driven
* Numbered stored procedure is the source of truth for line references
* Diagnosis is the primary semantic interpretation artifact
* Traceability bridges diagnosis and design
* Design must prepare reuse-aware code generation
* Final code generation must respect the existing codebase
* Determinism, auditability and incremental delivery are mandatory

## Hard Constraints

* Do not invent business rules
* Do not silently simplify legacy behavior
* Do not redesign the entire pipeline when only the next step is requested
* Do not promote LOW-confidence legacy logic to Domain Service
* Do not use semantic compression as the primary scalability strategy
* Prefer semantic chunking over aggressive context reduction
* Prefer explicit contracts over implicit inference
* Prefer safe incremental evolution over broad refactoring

## Diagnosis Rules

* Diagnosis is an analysis artifact, not a design artifact
* Diagnosis must preserve ambiguity when the code is ambiguous
* Unknown dependencies must remain explicitly unknown
* The diagnosis artifact must remain structurally valid and auditable

## Traceability Rules

* Traceability is not final design
* Traceability must map diagnosis semantics into structured blocks
* Traceability must preserve line ranges and semantic block identity
* Traceability may include targetLayerCandidate only as a preliminary hint

## Design Rules

* Design must use diagnosis as the semantic source
* Design must use traceability as the structural source
* Design must identify reuse, extension, and creation paths explicitly
* Design must respect confidence-driven rule placement
* Design must model transaction boundaries explicitly

## Codegen Direction

* Code generation must become aware of the evolving codebase
* Existing repositories, services, entities, and HQL/JPQL queries must be reused when possible
* Extension is preferred over duplication
* New artifacts must be justified

## Working Style Required From The Model

When helping with this project, always:

* diagnose before proposing
* explain root cause before suggesting change
* propose only the next safe incremental step
* keep solutions deterministic and testable
* preserve compatibility with current artifacts and contracts

Unless explicitly asked otherwise, do NOT:

* generate broad redesigns
* suggest speculative architecture
* mix multiple future steps in a single answer
* treat temporary artifacts as final architecture
