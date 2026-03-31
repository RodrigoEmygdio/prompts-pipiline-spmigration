Implement patch artifact support for diagnosis incremental correction.

Goal:
Standardize the persisted patch request and patch response artifacts for diagnosis repair flow.

Requirements:

1. Persist:

* work/<spId>/20-analysis/diagnosis.patch.request.md
* work/<spId>/20-analysis/diagnosis.patch.response.md

2. The patch request artifact must contain:

* metadata
* validation summary
* validation errors
* compact patch instructions
* optional relevant diagnosis excerpt
* optional relevant numbered stored procedure excerpt

3. The patch response artifact must contain:

* metadata
* raw patch output returned by the model

4. Standardize the patch response format expected from the LLM:

## PATCH

action: <insert_section | replace_section | rename_section>
target: "<section heading>"
position: after "<section heading>"   (only for insert_section)
replacement: "<new heading>"          (only for rename_section)

### CONTENT

<content here>

5. Introduce parsing and validation of patch responses before applying them

6. Patch response validation must fail fast if:

* action missing
* target missing
* required content missing
* insert_section missing position
* rename_section missing replacement

7. Keep the implementation deterministic and auditable

Goal:
Make diagnosis patch correction fully traceable through request/response artifacts
and prepare the pipeline for deterministic patch application.
