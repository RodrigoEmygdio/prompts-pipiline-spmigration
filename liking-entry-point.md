Refactor src/cli/index.ts to integrate it with the core context-builder.

Current problem:
- The CLI entrypoint still loads only canonical prompts directly
- It does not use the artifact-driven prompt compilation logic from src/core/context-builder.ts
- Therefore the pipeline cannot yet be tested end-to-end

Goal:
Turn src/cli/index.ts into the real runtime entrypoint for stage prompt compilation.

Requirements:

1. Parse CLI arguments as:
   - stage
   - spId

Expected usage:
   node dist/cli/index.js <stage> <spId>

2. Validate:
   - stage is provided
   - spId is provided
   - stage exists in STAGES

3. Keep:
   - ensureWorkDirectory()
   - validatePreconditions(stage)

4. Replace direct canonical prompt loading with:
   buildPrompt({ stage, spId })

5. For now, stage execution should:
   - compile the final prompt using context-builder
   - print a clear log message
   - print the compiled prompt to stdout

6. Do NOT implement model invocation yet
7. Do NOT implement artifact persistence yet
8. Keep the structure simple and coherent

9. Update all stage runner functions so they accept spId and use buildPrompt

10. Error messages should clearly distinguish:
   - invalid stage
   - missing spId
   - missing artifacts
   - failed prompt compilation

Goal:
Make the CLI capable of testing integrated stage prompt compilation end-to-end.