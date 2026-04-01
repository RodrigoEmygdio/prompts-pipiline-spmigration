Implement an incremental patch-based correction strategy for the diagnosis stage.

Context:

* diagnosis validation may fail for structural reasons
* sending the full original diagnosis prompt, full SP, and full previous diagnosis again would create oversized repair prompts
* oversized repair prompts increase latency and risk input loss
* the preferred strategy is minimal patch generation, with Node.js applying the patch incrementally

Goal:
Make diagnosis correction patch-based, minimal, and auditable.

Requirements:

1. Introduce diagnosis failure classification

Create a module such as:
src/core/diagnosis-failure-classifier.ts

It must classify diagnosis validation failures into:

* missing_section
* missing_line_references
* empty_section
* invalid_heading
* semantic_failure
* unknown

2. Introduce patchability rules

Patchable failures:

* missing_section
* missing_line_references
* empty_section
* invalid_heading

Non-patchable failures:

* semantic_failure
* unknown

3. Introduce a diagnosis patch prompt builder

Create a module such as:
src/core/diagnosis-patch-builder.ts

It must generate compact prompts that:

* use the numbered stored procedure as source of truth
* request only the missing or corrected section
* do not resend the full diagnosis prompt
* do not request full diagnosis regeneration
* do not generate code

4. Introduce a diagnosis patch applier

Create a module such as:
src/core/diagnosis-patch-applier.ts

Supported patch actions:

* insert_section
* replace_section
* rename_section

5. Patch response format

The LLM must return patch responses in this exact structure:

## PATCH

action: <insert_section | replace_section | rename_section>
target: "<section heading>"
position: after "<section heading>"   (only for insert_section)
replacement: "<new heading>"          (only for rename_section)

### CONTENT

<content here>

6. Diagnosis correction flow

When diagnosis validation fails:

* classify the failure
* if patchable:

  * build minimal patch prompt
  * invoke model
  * save patch request artifact
  * save patch response artifact
  * apply patch in Node.js
  * revalidate
* if not patchable:

  * fail the stage

7. Persist artifacts:

* diagnosis.validation.json
* diagnosis.patch.request.md
* diagnosis.patch.response.md
* diagnosis.patched.md

8. Keep the correction strategy deterministic
9. Do not implement blind retries
10. Do not regenerate the full diagnosis artifact during patch correction

Goal:
Correct diagnosis artifacts through minimal LLM-generated patches,
with Node.js performing deterministic merge and validation.
