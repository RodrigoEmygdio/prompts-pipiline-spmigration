Refactor the design stage so it consumes the traceability artifact as a primary structured input,
while preserving compatibility with the diagnosis artifact.

Context:

* The migration pipeline already produces:

  * numbered stored procedure
  * diagnosis artifact
  * traceability-map.json
* Diagnosis remains an important semantic artifact
* However, design now needs a stronger structured input layer
* The traceability map must become the primary structured bridge between diagnosis and design
* The design stage must remain semantically faithful to the legacy stored procedure
* The design stage must prepare future code generation with reuse-aware architecture decisions
* The design stage must remain compatible with future semantic chunking

Goal:
Make the design stage consume:

* diagnosis.md
* traceability-map.json
* numbered stored procedure
* project context pack

and produce a stronger architectural design artifact.

Input artifacts:

* work/<spId>/10-prepared/sp-numbered.sql
* work/<spId>/20-analysis/diagnosis.md
* work/<spId>/20-analysis/traceability-map.json
* prompts/context/project-context-pack.md
* canonical design prompt

Output artifacts:

* work/<spId>/30-design/design.md
* work/<spId>/meta/design-metrics.json

Requirements:

1. Update the design stage runtime flow so it consumes traceability-map.json
   as a primary structured artifact.

2. Keep diagnosis.md as an authoritative supporting semantic artifact.
   Do NOT remove diagnosis from the design stage.

3. Update the context builder for the design stage so it assembles sections in this order:

=== PROJECT CONTEXT PACK ===
=== STORED PROCEDURE (NUMBERED) ===
=== STRUCTURED DIAGNOSIS ===
=== TRACEABILITY MAP ===
=== CANONICAL STAGE INSTRUCTIONS ===

4. Refactor the canonical design prompt if necessary so it explicitly uses traceability map information.

The design stage must use traceability blocks to:

* identify logical migration units
* preserve line-level traceability
* understand dependencies
* understand preliminary target layer candidates
* understand block risks and side effects

5. The design artifact must use this exact structure:

# Architectural Design

## Use Case Overview

## Traceability-Driven Block Mapping

## Application Service Design

## Domain Service Design

## Repository Design

## Domain Model Design

## Transaction Boundaries

## Legacy Rule Placement

## Reuse and Extension Opportunities

## Required New Artifacts

## Design Risks and Open Decisions

6. Add this mandatory interpretation rule:

The design stage must treat the traceability map as the primary structured source
for block-level reasoning, but diagnosis remains the primary narrative semantic source.

This means:

* traceability map drives structure and block decomposition
* diagnosis provides semantic clarification and nuance
* numbered SP remains the final source of truth for lines and behavior

7. The section "Traceability-Driven Block Mapping" must explicitly map:

* traceability blockId
* line range
* block kind
* targetLayerCandidate
* final design interpretation

8. The section "Reuse and Extension Opportunities" must explicitly identify:

* existing artifacts likely reusable
* existing artifacts likely extensible
* artifacts that must be created from scratch
* why reuse is or is not possible

9. The section "Legacy Rule Placement" must explicitly follow:

* HIGH/MEDIUM confidence logic -> candidate Domain Service logic
* LOW confidence logic -> Application Service or Repository only

10. The section "Transaction Boundaries" must explicitly use:

* traceability transaction characteristics
* diagnosis transactional interpretation
* explicit ownership recommendation for the transaction boundary

11. Keep the first design implementation monolithic.
    Do NOT implement chunk-aware design orchestration yet.
    But keep the structure compatible with future chunked diagnosis/design flow.

12. Add or update a minimal design validator if useful.
    At minimum validate:

* output not empty
* required sections present
* traceability-driven block mapping section present
* reuse and extension opportunities section present
* risks and open decisions section present

13. Persist design metrics:

* stage
* spId
* model
* promptLength
* promptByteLength
* durationMs
* status
* attempt

14. Preserve existing pipeline contracts:

* buildPrompt
* invokeModel
* normalize
* validate
* save artifact
* update manifest on success

15. Important architectural constraints:

* Do NOT generate code in the design stage
* Do NOT invent architecture unsupported by diagnosis or traceability
* Do NOT ignore reuse opportunities when traceability or diagnosis indicate them
* Do NOT promote LOW-confidence legacy logic to Domain Service

Goal:
Make design the next robust executable stage of the migration pipeline,
using traceability-map.json as a structured bridge between diagnosis and architecture,
while remaining compatible with future semantic chunking and reuse-aware code generation.
