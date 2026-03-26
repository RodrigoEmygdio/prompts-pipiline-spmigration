Implement src/core/model-adapter.ts using the official OpenCode JS/TS SDK.

Goal:
Turn the current placeholder model adapter into a real integration layer
that invokes OpenCode programmatically through a running OpenCode server.

Requirements:

1. Use the official package:
   @opencode-ai/sdk

2. Use client-only mode:
   createOpencodeClient({ baseUrl })

3. Read OpenCode connection settings from src/core/config.ts

4. Export a function:

   invokeModel(input: { stage: Stage; prompt: string }): Promise<string>

5. Model selection must be stage-driven:
   - numbering -> config.models.numbering
   - diagnosis -> config.models.diagnosis
   - design -> config.models.design
   - codegen -> config.models.codegen
   - review -> config.models.review

6. If agent configuration exists for the stage, pass it through

7. The adapter must:
   - create or use a session
   - send the compiled prompt
   - return the final text output only

8. Keep the implementation modular and deterministic
9. Throw explicit errors on connection or execution failure
10. Do not implement HTTP calls manually
11. Do not shell out to opencode run

Also refactor the numbering stage to use this real adapter.

For output normalization:
- keep normalization minimal
- only trim output
- remove markdown code fences if present
- remove only obvious leading editorial prefix before the first numbered line
- do not add aggressive regex cleanup

Goal:
Make numbering the first truly executable LLM-backed stage using OpenCode as the runtime.