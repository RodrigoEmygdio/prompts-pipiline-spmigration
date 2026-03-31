You must generate a minimal patch for a diagnosis artifact.

The diagnosis artifact is incomplete.
A required section is missing.

Rules:

* Use the numbered stored procedure as the source of truth
* Generate ONLY the missing section
* Do NOT rewrite the full diagnosis
* Do NOT generate code
* Do NOT add new business assumptions
* Preserve ambiguity where the code is ambiguous

Missing section:

## Line-by-Line Classification

Existing diagnosis headings:

* ## Functional Summary
* ## Hidden or Implicit Rules
* ## External Dependencies
* ## DDD-Oriented Interpretation
* ## Migration Risks
* ## Uncertainty and Open Questions

Return the patch in this exact format:

## PATCH

action: insert_section
target: "## Line-by-Line Classification"
position: after "## Functional Summary"

### CONTENT

## Line-by-Line Classification

<write only this section here>

=== NUMBERED STORED PROCEDURE === <paste here>
