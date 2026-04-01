Refactor the diagnosis stage to support validation-aware correction and artifact-specific metrics.

Context:

* diagnosis already invokes the model and validates the normalized output
* currently, if assertDiagnosisValid fails, the stage aborts immediately
* this is correct for safety, but incomplete for pipeline resilience
* diagnosis requires a controlled correction strategy
* blind retries are NOT acceptable
* retries must be guided by validation errors

Goal:
Make the diagnosis stage resilient while preserving strict artifact quality control.

Requirements:

1. Keep the current safety rule:
   invalid diagnosis output must never be accepted silently

2. Implement a controlled repair strategy for diagnosis:

Execution policy:

* attempt 1: normal diagnosis generation
* validate normalized output
* if valid: succeed
* if invalid: run exactly one repair attempt
* attempt 2: generate a corrected diagnosis using validation errors
* validate again
* if still invalid: fail the stage

3. The repair attempt must use:

* the original compiled diagnosis prompt
* the previous normalized diagnosis output
* the validation errors returned by diagnosis-validator

4. Create a diagnosis repair prompt builder, for example:
   src/core/diagnosis-repair.ts

It should instruct the model to:

* correct the diagnosis artifact structure
* preserve existing valid content
* add missing required sections
* add missing line references when absent
* not generate code
* not redesign the artifact scope

5. Persist intermediate artifacts for diagnosis:

* work/<spId>/20-analysis/diagnosis.raw.md
* work/<spId>/20-analysis/diagnosis.normalized.md
* work/<spId>/20-analysis/diagnosis.validation.json

If repair is attempted, also persist:

* work/<spId>/20-analysis/diagnosis.repair.raw.md
* work/<spId>/20-analysis/diagnosis.repair.normalized.md

6. Upgrade diagnosis-metrics.json so it includes diagnosis-specific validation metadata:

Include:

* stage
* spId
* model
* attempt
* providerDurationMs
* promptLength
* rawOutputLength
* normalizedOutputLength
* status
* validation:

  * passed
  * missingSections
  * hasLineReferences
  * errors

7. Do NOT implement blind generic retries

8. Do NOT retry more than once

9. Keep the stage deterministic and auditable

10. Preserve existing pipeline contracts:

* buildPrompt
* invokeModel
* normalize
* validate
* save artifact
* update manifest only on success

Goal:
Make diagnosis resilient through validation-aware correction while keeping strict control over artifact quality and auditability.
