# Traceability Module - Purpose, Principles, and Invariants

## 1. Purpose

The Traceability module exists to create a deterministic bridge between the **diagnosis stage** and the **design stage** of the legacy stored procedure migration pipeline.

Its purpose is to transform structured diagnosis output into a **traceable semantic map** that preserves:

* line-level linkage to the numbered stored procedure
* semantic grouping of migration-relevant logic blocks
* dependency visibility
* side-effect visibility
* transaction-related visibility
* preliminary architectural routing hints

The module does **not** perform design, code generation, or business reinterpretation.

It exists to answer this question:

**"What are the semantically relevant blocks of the stored procedure, where do they come from, and what do they appear to represent based only on diagnosis evidence?"**

---

## 2. Position in the Pipeline

The Traceability module sits strictly between:

* **Diagnosis**
* **Design**

Pipeline order:

1. init
2. numbering
3. diagnosis
4. traceability
5. design
6. codegen
7. review

The module consumes:

* numbered stored procedure
* diagnosis artifact

The module produces:

* `traceability-map.json`

The design stage may use traceability as its primary structured block-level input, but diagnosis remains the primary narrative semantic source.

---

## 3. Core Responsibility

The Traceability module must:

* identify semantically relevant blocks from diagnosis
* preserve line-range traceability to the numbered stored procedure
* organize discovered logic into deterministic structured blocks
* expose block-level metadata useful for design
* remain neutral with respect to final architecture

The Traceability module must **not**:

* generate final architecture
* decide final class structure
* create services, repositories, or entities
* invent hidden business meaning
* infer behavior not grounded in diagnosis
* silently succeed with an empty traceability map when diagnosis is valid

---

## 4. Design Philosophy

The module follows these principles:

### 4.1 Diagnosis-driven

Diagnosis is the primary semantic source.

Traceability must not reinterpret diagnosis beyond what is explicitly supported.

### 4.2 Numbered-SP anchored

The numbered stored procedure is the source of truth for line ranges.

If diagnosis and numbered SP disagree on line boundaries, the numbered SP wins.

### 4.3 Deterministic

For the same inputs, the module must produce equivalent outputs.

No randomness, hidden heuristics, or speculative branching is allowed.

### 4.4 Non-generative

The module does not "design" or "optimize" anything.

It only structures already discovered semantics.

### 4.5 Explicit uncertainty

If the module cannot determine something safely, it must use explicit fallback values such as:

* `kind: "unknown"`
* `targetLayerCandidate: "undecided"`
* `confidence: "unknown"`
* dependency type `"unknown"`

Uncertainty must be explicit, never hidden.

---

## 5. What a Traceability Block Represents

A traceability block is a **semantic migration unit** extracted from diagnosis and anchored to a line range in the numbered stored procedure.

A block is not necessarily:

* a method
* a class
* a final domain rule
* a final repository operation

It is instead a **semantic unit of concern** useful for later design decomposition.

Examples:

* business logic block
* operational logic block
* data access block
* side-effect block
* external dependency block

---

## 6. Canonical Semantics

### 6.1 Block kinds

Traceability block kinds must remain aligned with diagnosis taxonomy:

* `business_logic`
* `operational_logic`
* `data_access`
* `side_effect`
* `external_dependency`
* `unknown`

These are semantic categories, not final architecture decisions.

### 6.2 Operational refinement

Operational logic may optionally be refined with an operational subtype, such as:

* orchestration
* transaction_control
* iteration
* exception_flow
* declaration
* validation
* none

This refinement is optional and must only be used when diagnosis provides enough signal.

### 6.3 Confidence inheritance

Confidence in traceability must be inherited from diagnosis whenever possible.

Confidence represents confidence in semantic interpretation, not business truth.

### 6.4 Architectural routing hints

`targetLayerCandidate` is only a preliminary routing hint.

It is not a final design commitment.

Allowed examples:

* application_service
* domain_service
* repository
* domain_model
* external_dependency
* undecided

---

## 7. Module Invariants

These invariants must always hold.

### Invariant 1 - No empty success

If diagnosis is valid and traceability extraction produces zero blocks, the module must fail explicitly.

It must never silently produce a successful empty map.

### Invariant 2 - Every block must be line-anchored

Every block must have:

* `startLine`
* `endLine`

No block may exist without an SP line anchor.

### Invariant 3 - Every block must have semantic identity

Every block must have:

* `blockId`
* `order`
* `kind`
* `summary`

### Invariant 4 - No speculative enrichment

Dependencies, confidence, transaction flags, and target layer candidates must only be enriched when diagnosis provides enough evidence.

### Invariant 5 - No final design leakage

Traceability must not encode final design decisions.

It may suggest routing hints, but must not prescribe final services, repositories, or entities.

### Invariant 6 - Stable ordering

Blocks must be deterministically ordered by:

1. `startLine`
2. `order`

### Invariant 7 - Explicit unknowns

When information is insufficient, unknown values must be emitted explicitly rather than guessed.

### Invariant 8 - Summary consistency

Traceability summary counts must match the actual block list.

### Invariant 9 - Section dependency

The module must treat **Line-by-Line Classification** as the primary block extraction source.

If that section is missing or unparsable, this must be visible in debug/error artifacts.

### Invariant 10 - Debuggability

Traceability extraction must be observable through a debug artifact during the current MVP phase.

---

## 8. Extraction Rules

### 8.1 Primary extraction source

Primary block extraction must come from:

* `## Line-by-Line Classification`

This section is the canonical source for block-level traceability.

### 8.2 Secondary enrichment sources

Other diagnosis sections may enrich blocks:

* Hidden or Implicit Rules
* External Dependencies
* DDD-Oriented Interpretation
* Migration Risks
* Uncertainty and Open Questions

These sections enrich but do not replace the primary extraction source.

### 8.3 Fallback behavior

If enrichment is not possible, the block must still be created if the primary extraction is valid.

The module must prefer partial explicit truth over fabricated completeness.

---

## 9. What the Module Must Never Do

The Traceability module must never:

* generate Java code
* generate HQL/JPQL
* create class names
* propose final repositories
* propose final services
* collapse multiple uncertain behaviors into one artificial rule
* upgrade low-confidence legacy behavior into domain truth
* silently ignore parse failures
* hide missing diagnosis coverage

---

## 10. Expected Artifacts

### Required output

* `traceability-map.json`

### Temporary but recommended during MVP

* `traceability-extraction-debug.json`

The debug artifact should expose:

* sections found
* classification section detection
* raw classification entries
* parsed classification entries
* block enrichment usage
* blocks created
* warnings
* errors

---

## 11. Quality Criteria

The Traceability module is considered correct when:

* it creates non-empty blocks from valid diagnosis
* each block is line-traceable
* block kinds align with diagnosis taxonomy
* unknowns remain explicit
* output is deterministic
* summary statistics are correct
* design can use the artifact as a stable structured input

The module is considered incorrect when:

* it succeeds with zero blocks
* it invents semantics
* it hides uncertainty
* it encodes final design prematurely
* it loses line-level traceability

---

## 12. Guiding Rule

The Traceability module does not explain, design, or generate.

It **structures discovered semantics into stable, line-traceable migration units**.

That is its only job.
