Refactor the migration pipeline to replace the stages "seed" and "complete"
with a single stage called "codegen".

Requirements:
- Update the stage enum/list and dependencies
- Update validation rules
- Update command files under .opencode/commands
- Update canonical prompt files under prompts/canonical
- Keep review depending on codegen
- Introduce a configuration module for:
  - targetProjectRoot
  - artifactsRoot
  - contextPackPath
- Keep work/ for intermediate artifacts only
- Codegen must be designed to write final code into the target project root
- Do not implement final code generation logic yet
- Only refactor the structure, contracts and placeholders