You are working inside a new Node.js/TypeScript project that will orchestrate
a staged stored procedure migration workflow using OpenCode.

Goal:
Create the initial project structure for a PoC that automates part of the
legacy stored procedure migration process.

Technical decisions:
- Use Node.js + TypeScript
- Do NOT implement an OpenCode plugin yet
- Use custom OpenCode commands plus Node helper scripts
- The helper scripts will resolve artifact paths, validate stage preconditions,
  load canonical prompts from disk, and render the final prompt for OpenCode
- The workflow stages are:
  1) init
  2) numbering
  3) diagnosis
  4) design
  5) seed
  6) complete
  7) review

Required output:
Create all initial files and folders needed for the first iteration, including:

1. package.json
2. tsconfig.json
3. .gitignore
4. AGENTS.md
5. .opencode/commands/ with one markdown command file per stage
6. .opencode/skills/legacy-sp-migration/SKILL.md
7. prompts/canonical/ with one prompt file per stage
8. src/core and src/cli structure
9. work/.gitkeep

Implementation requirements:
- Keep the code minimal but production-like
- Use clear separation between path resolution, artifact loading,
  prompt loading, and stage validation
- Add TODO markers where stage-specific behavior will be implemented later
- Do NOT implement the full workflow logic yet
- Implement only the skeleton and contracts
- Ensure the project can build with TypeScript
- Add npm scripts for build and dev
- Prefer ESM or a consistent TS module strategy and keep it simple

Important architectural rules:
- Each stage must be self-sufficient
- No stage may rely on chat memory
- All inputs must come from disk artifacts
- Every stage must validate its required inputs before running
- Canonical prompts stored in prompts/canonical are the source of truth
- OpenCode command files should call the built Node CLI via shell injection

Create the files directly in the repository.
