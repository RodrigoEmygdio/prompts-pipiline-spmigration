Implement a deterministic diagnosis patch applier module.

Goal:
Apply LLM-generated diagnosis patches safely and incrementally using structured patch responses.

Create module:
src/core/diagnosis-patch-applier.ts

Responsibilities:

1. Load:

* diagnosis.normalized.md
* diagnosis.patch.response.md
* diagnosis.validation.json

2. Extract PATCH block from patch.response.md

Expected format:

## PATCH

action: <insert_section | replace_section | rename_section>
target: "<section heading>"
position: after "<section heading>" (only for insert_section)
replacement: "<new heading>" (only for rename_section)

### CONTENT

<section content>

3. Parse patch metadata and content into structured object

4. Validate patch structure

Reject patch if:

* action missing
* target missing
* content missing when required
* insert_section missing position
* rename_section missing replacement

5. Validate patch action matches validation report expectations

Example:
If validation.json expects insert_section,
reject replace_section

6. Parse diagnosis markdown sections

Sections start with:

## Heading

Extract heading boundaries and content

7. Apply patch action

Supported actions:

insert_section:
insert new section after target position section

replace_section:
replace full content of target section

rename_section:
replace heading text only

8. Fail fast if target section does not exist

9. Rebuild markdown after patch

10. Perform structural validation

Ensure:
no duplicate headings
no missing required headings
markdown not empty

11. Persist result:

work/<spId>/20-analysis/diagnosis.patched.md

12. Return patched markdown

Constraints:

Do not regenerate full diagnosis
Do not allow multiple patch actions
Do not modify sections outside patch scope
Keep behavior deterministic and auditable

Goal:
Provide safe incremental correction of diagnosis artifacts through structured patch application.
