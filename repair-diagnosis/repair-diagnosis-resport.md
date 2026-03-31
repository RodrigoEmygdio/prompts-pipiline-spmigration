Refactor diagnosis validation output so it produces a structured validation report JSON.

Goal:
Replace simple string-based validation output with a structured diagnosis.validation.json
that can support automated decision-making, patch generation, and auditability.

Requirements:

1. Define a structured validation report shape for diagnosis.

Suggested output file:
work/<spId>/20-analysis/diagnosis.validation.json

2. The report must include:

* stage
* spId
* artifactPath
* validatedAt
* passed
* summary
* errors
* repairStrategy

3. Summary must include:

* totalErrors
* patchableErrors
* nonPatchableErrors
* hasLineReferences
* requiredSectionsPresent

4. Each validation error must include:

* code
* message
* patchable
* severity
* targetSection
* expectedAction
* evidence

5. Supported error codes:

* empty_output
* missing_section
* empty_section
* missing_line_references
* invalid_heading
* structural_order
* semantic_failure
* unknown

6. Repair strategy must include:

* recommendedMode
* maxRepairAttempts
* candidatePatchTargets

7. Do NOT embed the full diagnosis artifact in the JSON report.
   Use compact evidence only.

8. Keep the validator deterministic and minimal.

Goal:
Make diagnosis validation machine-readable and compatible with future patch-based correction flow.
