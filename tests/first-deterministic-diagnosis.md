Add the first deterministic diagnosis patch fixtures and unit tests.

Goal:
Create a small but complete initial fixture set to validate diagnosis patch application behavior.

Requirements:

1. Create diagnosis fixtures under:

tests/fixtures/diagnosis/

2. Add at least these fixtures:

* diagnosis.normalized.missing-section.md
* diagnosis.validation.missing-section.json
* diagnosis.patch.insert-section.md
* diagnosis.normalized.missing-line-refs.md
* diagnosis.patch.replace-section.md

3. Also add the recommended rename-section fixtures if useful:

* diagnosis.normalized.invalid-heading.md
* diagnosis.patch.rename-section.md

4. The fixtures must be small, readable, and deterministic

5. Add unit tests for diagnosis-patch-applier covering:

* insert_section
* replace_section
* rename_section (if fixture added)
* invalid target section
* action not allowed by validation report

6. Add unit tests for diagnosis-validator covering:

* missing required section
* missing line references
* invalid heading
* valid diagnosis

7. Keep tests local and deterministic
8. Do NOT call the real provider
9. Use fixture files as the source of truth for behavior

Goal:
Validate the diagnosis incremental patch mechanism even when the real LLM does not naturally produce broken outputs.
