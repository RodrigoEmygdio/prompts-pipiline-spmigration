# AGENTS.md
## Legacy Stored Procedure Migration Pipeline

This repository contains a deterministic staged pipeline used to migrate
legacy stored procedures into structured Java implementations using LLM-assisted analysis,
design, and code generation.

The OpenCode runtime operates as a workflow orchestrator. It must follow the rules
described in this document strictly.

This file defines the architectural contract for all agents operating in this repository.

---

# PRIMARY OBJECTIVE

Transform a stored procedure into a structured Java implementation using a staged workflow:

init
→ numbering
→ diagnosis
→ design
→ codegen
→ review

Each stage produces artifacts persisted on disk.

No stage may depend on chat memory.

---

# EXECUTION MODEL

This pipeline is **artifact-driven**, not conversation-driven.

Every stage:

- reads inputs from disk
- produces outputs to disk
- validates dependencies before execution
- fails fast if artifacts are missing

Agents must never assume previous context implicitly.

---

# PIPELINE STAGES

## init

Purpose:

Create migration workspace for a stored procedure.

Outputs:

work/<SP_ID>/ directory structure
meta/manifest.json

---

## numbering

Inputs:

00-input/sp.sql

Outputs:

10-prepared/sp-numbered.sql

Purpose:

Create a numbered version of the stored procedure preserving formatting and structure.

---

## diagnosis

Inputs:

10-prepared/sp-numbered.sql

Outputs:

20-analysis/diagnosis.md

Purpose:

Produce structured semantic interpretation of the stored procedure.

Includes:

functional summary
rule classification
dependencies
confidence levels
side effects
DDD hints

---

## design

Inputs:

10-prepared/sp-numbered.sql
20-analysis/diagnosis.md
prompts/context/project-context-pack.md

Outputs:

30-design/design.md

Purpose:

Produce an explicit architectural design describing:

Application Service
Domain Service(s)
Repository responsibilities
Domain Models
transaction boundaries
orchestration flow

Design output is authoritative.

---

## codegen

Inputs:

10-prepared/sp-numbered.sql
20-analysis/diagnosis.md
30-design/design.md
prompts/context/project-context-pack.md

Outputs:

Java source files written directly into the target project

and

work/<SP_ID>/meta/codegen-manifest.json

Purpose:

Generate final implementation consistent with the approved design.

Code generation must:

NOT redesign architecture
NOT invent rules
NOT omit layers defined in design

---

## review

Inputs:

all previous artifacts
generated Java files

Outputs:

50-review/review.md

Purpose:

Verify semantic equivalence between stored procedure and generated Java implementation.

Includes:

coverage validation
risk detection
missing rules
confidence analysis

---

# ARTIFACT STORAGE MODEL

Artifacts are stored under:

work/<SP_ID>/

Structure:

00-input/
10-prepared/
20-analysis/
30-design/
50-review/
meta/

Artifacts are authoritative pipeline state.

Agents must never bypass artifact storage.

---

# PROMPT SOURCE OF TRUTH

Canonical prompts are stored under:

prompts/canonical/

Agents must never inline prompt logic inside code.

Prompt selection must be stage-driven.

---

# CONTEXT PACK

Global project rules are defined in:

prompts/context/project-context-pack.md

Agents must always inject this context when executing:

design
codegen
review

---

# DATA ACCESS POLICY

JPA is mandatory.

JdbcTemplate is allowed ONLY for:

external stored procedures
database functions
legacy dependencies outside the migrated stored procedure

JdbcTemplate must never replace JPA for standard persistence logic.

---

# ARCHITECTURAL LAYER RULES

Application Service:

entrypoint of migrated use case
orchestrates repositories and domain services
may contain LOW-confidence legacy logic

Domain Service:

contains HIGH or MEDIUM confidence business rules only
must not access repositories
must not manage transactions

Repository Layer:

uses JPA for persistence
may use JdbcTemplate only for external dependencies

Domain Model:

must reflect entities described in design
must not duplicate existing project entities

---

# STAGE DEPENDENCY RULES

Stage execution order is strict:

init
→ numbering
→ diagnosis
→ design
→ codegen
→ review

Agents must validate dependencies before execution.

If a dependency artifact is missing:

execution must stop immediately.

---

# TARGET PROJECT INTEGRATION

Generated Java code must be written into:

targetProjectRoot

configured in:

src/core/config.ts

Artifacts must NOT be written inside work/

work/ is reserved for pipeline state only.

---

# CLI ENTRYPOINT CONTRACT

Single CLI entrypoint:

src/cli/index.ts

Responsibilities:

parse stage argument
validate dependencies
build prompt context
dispatch execution

Stage handlers live under:

src/stages/

---

# CONTEXT BUILDER RESPONSIBILITY

Context assembly must be handled by:

src/core/context-builder.ts

Responsibilities:

load canonical prompt
load required artifacts
load context pack
assemble final prompt
fail if artifact missing

Agents must not assemble prompts manually.

---

# DETERMINISM REQUIREMENT

Pipeline execution must be reproducible.

Given:

same stored procedure
same artifacts
same prompts

execution must produce equivalent output.

Agents must not introduce randomness into pipeline state.

---

# MODEL RESPONSIBILITY SPLIT

Diagnosis:

semantic model preferred

Design:

semantic model preferred

Codegen:

coder model preferred

Review:

semantic validation model preferred

---

# FAILURE CONDITIONS

Execution must stop if:

required artifact missing
design incomplete
context pack unavailable
target project root unresolved
stage order violated

Agents must never continue after failure detection.

---

# PROHIBITED ACTIONS

Agents must NOT:

skip stages
rewrite artifacts silently
invent architecture
change persistence policy
modify canonical prompts
write generated code inside work/
execute stages out of order

---

# REQUIRED BEHAVIOR

Agents must:

follow stage contract strictly
read artifacts from disk
write artifacts to disk
respect canonical prompts
respect context pack rules
respect design authority

---

# FINAL PRINCIPLE

This pipeline is artifact-driven.

Artifacts define truth.

Prompts define behavior.

Stages define execution order.

Agents execute — they do not decide architecture.
