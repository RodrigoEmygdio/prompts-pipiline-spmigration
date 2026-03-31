Implement the second operational stage of the migration pipeline: diagnosis.

Context:

* init is already operational
* numbering is already the first real executable LLM-backed stage
* diagnosis is the next critical stage
* diagnosis must produce an authoritative structured analysis artifact
* diagnosis must remain semantically faithful to the stored procedure
* diagnosis must NOT generate code
* diagnosis must preserve ambiguity when the code is ambiguous
* aggressive prompt compaction is NOT acceptable
* future support for chunking may be added later, but this first version must remain monolithic

Goal:
Make diagnosis the second fully operational stage of the migration pipeline.

Input artifact:
work/<spId>/10-prepared/sp-numbered.sql

Output artifacts:

* work/<spId>/20-analysis/diagnosis.md
* work/<spId>/meta/diagnosis-metrics.json

Pipeline contract:
buildPrompt -> invokeModel -> normalize -> validate -> save artifact

Requirements:

1. Implement the diagnosis stage execution in the CLI/runtime flow

The stage must:

* load the compiled prompt using context-builder
* invoke the provider adapter
* normalize the output
* validate the diagnosis artifact
* persist diagnosis.md
* persist diagnosis-metrics.json
* update meta/manifest.json to mark diagnosis as completed

2. Keep diagnosis monolithic for now

Do NOT implement chunking yet.
Do NOT split the stored procedure.
Do NOT add diagnosis-part files yet.

3. The diagnosis artifact must use this exact section structure:

# Structured Diagnosis

## Functional Summary

## Line-by-Line Classification

## Hidden or Implicit Rules

## External Dependencies

## DDD-Oriented Interpretation

## Migration Risks

## Uncertainty and Open Questions

4. Update the canonical diagnosis prompt if necessary so it enforces:

* no code generation
* no architecture redesign
* no invented behavior for unknown dependencies
* explicit line references
* explicit uncertainty recording
* preservation of ambiguity
* output only the structured diagnosis artifact

5. Implement a diagnosis validator module

Suggested file:
src/core/diagnosis-validator.ts

Minimum validation rules:

* output must not be empty
* all required sections must exist
* line references must appear
* migration risks section must exist
* uncertainty/open questions section must exist

The validator should fail fast with explicit error messages.

6. Persist diagnosis metrics in:
   work/<spId>/meta/diagnosis-metrics.json

Include at least:

* stage
* spId
* model
* promptLength
* promptByteLength
* durationMs
* attempt
* status
* errorMessage (if any)

7. Update manifest.json

If diagnosis succeeds:

* append "diagnosis" to completedStages if not already present

8. Keep the rest of the pipeline unchanged

Do NOT redesign:

* context-builder
* artifact paths
* provider-adapter contract
* stage ordering
* pipeline structure

9. Implementation style

* keep code minimal and production-like
* explicit logging is allowed
* fail fast on invalid output
* preserve deterministic stage behavior

10. Important semantic rule

Diagnosis is an analysis artifact, not a design artifact.
The DDD-Oriented Interpretation section is only an interpretation of likely layer placement.
It must NOT become final architecture design.

Goal:
Make diagnosis the second executable stage of the migration pipeline,
producing a reusable and auditable diagnosis.md artifact that can later feed the design stage.
