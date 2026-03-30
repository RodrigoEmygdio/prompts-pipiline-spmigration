Refactor the provider execution pipeline to support stage-aware execution policies.

Context:
Smoke tests showed that prompt execution time increases significantly with prompt size.
The same stage executed multiple times had variable duration, from around 81 seconds
to longer than the configured timeout.
This behavior is expected to become more critical in later stages such as diagnosis,
design and codegen, where prompts will be much larger.

Goal:
Make the pipeline robust against high and variable LLM latency.

Requirements:

1. Introduce stage-aware timeout configuration in config.ts

Example:
- numbering
- diagnosis
- design
- codegen
- review

Each stage must be able to define its own timeoutMs.

2. Add retry support in provider-adapter.ts

Rules:
- retry once on:
  - timeout
  - network failure
  - HTTP 5xx
- do not retry on 4xx

3. Add execution metrics persistence for each stage

Create a metrics artifact, for example:
work/<spId>/meta/<stage>-metrics.json

Include:
- stage
- spId
- model
- promptLength
- promptByteLength
- durationMs
- attempt
- status
- errorMessage (if any)

4. Add logging for:
- stage
- model
- prompt length
- timeout configured
- attempt number
- execution duration

5. Do NOT redesign context-builder
6. Do NOT redesign artifact paths
7. Do NOT redesign stage contracts

8. Keep implementation deterministic and minimal

Goal:
Make the pipeline operationally robust for large prompts and variable provider latency.
