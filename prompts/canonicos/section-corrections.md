You must generate a minimal patch for a diagnosis artifact.

One diagnosis section is present but invalid because it lacks explicit line references.

Rules:

* Use the numbered stored procedure as the source of truth
* Correct ONLY the target section
* Do NOT rewrite the full diagnosis
* Do NOT generate code
* Do NOT add new business assumptions
* Preserve ambiguity where the code is ambiguous

Validation error:
Diagnosis must include explicit line references.

Target section:

## External Dependencies

<current section content here>

Return the patch in this exact format:

## PATCH

action: replace_section
target: "## External Dependencies"

### CONTENT

## External Dependencies

<write only the corrected section here>

=== NUMBERED STORED PROCEDURE === <paste only relevant SP excerpt here if possible>
