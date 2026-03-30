Refactor the current model execution strategy to remove dependency on OpenCode runtime
and replace it with a direct provider adapter using the corporate OpenAI-compatible API.

Context:

The migration pipeline currently depends on OpenCode SDK/server integration,
but this environment cannot reliably execute large prompts through OpenCode.

The corporate provider exposes an OpenAI-compatible Chat Completions endpoint 

Authentication:
Authorization: Bearer <API_KEY>

The adapter must call this endpoint directly using HTTP.

Goal:

Create a deterministic provider adapter that replaces OpenCode runtime usage
while preserving the existing pipeline contract.

Required implementation:

Create:

src/core/provider-adapter.ts

Expose:

async function invokeModel(input: {
stage: Stage
prompt: string
}): Promise<string>

Behavior:

1. Select model based on stage from config.ts

Example:

config.models.numbering
config.models.diagnosis
config.models.design
config.models.codegen
config.models.review

2. Read API key from environment variable:



Fail fast if missing.

3. Call endpoint:



Headers:

Authorization: Bearer <API_KEY>
Content-Type: application/json

Body:

{
"model": selectedModel,
"messages": [
{
"role": "user",
"content": prompt
}
],
"temperature": 0
}

4. Extract response from:

choices[0].message.content

Return this value as string.

5. Implement timeout:

default timeout = 120000 ms

Fail if exceeded.

6. Implement retry strategy:

retry once if:

- network failure
- HTTP 5xx

Do NOT retry on 4xx.

7. Error handling must include:

stage
model
status code
response body (if available)

Example error message:

Provider execution failed for stage=numbering model=qwen3-coder-next status=500

8. Logging:

Log:

stage
model
prompt length
request start
request end
execution duration

Do NOT log prompt content.

9. Validate response:

If response missing:

choices[0].message.content

throw explicit error:

Model returned empty response

10. Do NOT modify:

context-builder.ts
artifact paths
stage orchestration
pipeline structure

11. Preserve pipeline contract:

buildPrompt -> invokeModel -> normalize -> validate -> save artifact

12. Implementation constraints:

Use native fetch or axios

Prefer native fetch if Node runtime supports it

Avoid streaming mode

Use synchronous response mode only

Goal:

Make numbering the first fully operational LLM-backed stage
using the corporate OpenAI-compatible provider instead of OpenCode runtime.