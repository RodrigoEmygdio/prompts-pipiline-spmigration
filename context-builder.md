Implement src/core/context-builder.ts for the legacy stored procedure migration pipeline.

Goal:
Create a deterministic prompt assembly module that builds the final prompt
for each pipeline stage using artifacts stored on disk, the canonical prompt,
and the project context pack.

Context:
This project is artifact-driven.
The context builder must NOT call models, save results, or execute stages.
Its responsibility is ONLY to assemble the final prompt text for a given stage.

Requirements:

1. Export a public function:

   buildPrompt(input: { stage: Stage; spId: string }): Promise<string>

2. The module must support these stages:
   - init
   - numbering
   - diagnosis
   - design
   - codegen
   - review

3. For each stage, assemble prompt sections in deterministic order.

Expected sections by stage:

- init:
  - canonical prompt

- numbering:
  - canonical prompt
  - raw stored procedure

- diagnosis:
  - canonical prompt
  - numbered stored procedure

- design:
  - project context pack
  - numbered stored procedure
  - approved structured diagnosis
  - canonical prompt

- codegen:
  - project context pack
  - numbered stored procedure
  - approved structured diagnosis
  - approved architectural design
  - canonical prompt

- review:
  - project context pack
  - numbered stored procedure
  - approved structured diagnosis
  - approved architectural design
  - generated code context
  - canonical prompt

4. Render the final prompt using explicit section headers, for example:

   === PROJECT CONTEXT PACK ===
   <content>

   === STORED PROCEDURE (NUMBERED) ===
   <content>

5. Use existing modules where appropriate:
   - paths.ts
   - prompts.ts
   - artifacts.ts
   - config.ts

6. If a required artifact is missing, throw an explicit error.
   No silent fallback, no guessing.

7. Keep the implementation modular.
   Use internal helper functions such as:
   - getPromptSectionsForStage
   - loadSectionContent
   - renderSections

8. Do NOT implement model execution.
9. Do NOT save rendered prompts to disk yet.
10. Keep the code minimal, typed, and coherent with the current project structure.

Important:
If generated code context for the review stage is not fully implemented yet,
create a placeholder helper function with a TODO and a clear contract,
without breaking the module structure.