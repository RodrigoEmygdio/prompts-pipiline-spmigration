Implement unit tests for the diagnosis correction pipeline components.

Context:

* The LLM currently produces good diagnosis outputs, so real model behavior is not sufficient
  to validate the Node.js correction logic
* We need deterministic tests for the runtime components, independent from the provider
* Tests must validate the pipeline behavior by contract using fixtures and mocks

Goal:
Add a first solid test suite for the diagnosis stage infrastructure.

Requirements:

1. Add a test framework suitable for Node.js + TypeScript
   Preferred:

* Vitest

2. Create a test structure such as:

tests/
unit/
fixtures/

3. Add fixtures for diagnosis validation and patch flow, for example:

tests/fixtures/diagnosis/
valid-diagnosis.md
missing-section.md
missing-line-refs.md
invalid-heading.md
validation-report.missing-section.json
patch.insert-section.md
patch.replace-section.md
patch.rename-section.md

4. Implement unit tests for:

* src/core/diagnosis-validator.ts
* src/core/diagnosis-failure-classifier.ts
* src/core/diagnosis-patch-builder.ts
* src/core/diagnosis-patch-applier.ts

5. Validator tests must cover:

* valid diagnosis passes
* empty diagnosis fails
* missing section fails
* missing line references fail
* structured validation report is produced correctly

6. Failure classifier tests must cover:

* missing_section -> patchable
* missing_line_references -> patchable
* semantic_failure -> non-patchable
* recommendedMode is computed correctly

7. Patch builder tests must cover:

* minimal patch prompt for missing section
* minimal patch prompt for missing line references
* no oversized prompt behavior
* patch prompt includes only relevant context

8. Patch applier tests must cover:

* insert_section
* replace_section
* rename_section
* invalid target section
* patch action not allowed by validation report
* patch does not modify unrelated sections

9. Add at least one integration-lite test for the diagnosis stage runtime:

* mock invokeModel
* simulate invalid diagnosis output
* simulate patch response
* verify patched diagnosis is saved correctly

10. Keep tests deterministic and local
11. Do NOT call the real provider
12. Use mocks/stubs for model invocation

Goal:
Validate that the Node.js diagnosis correction pipeline behaves correctly,
even when the real LLM does not naturally produce failure cases.
