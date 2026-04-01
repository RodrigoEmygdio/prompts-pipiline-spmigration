Define the contract and initial TypeScript model for the traceability discovery artifact.

Goal:
Introduce a structured traceability-map.json artifact that bridges diagnosis and design.

Requirements:

1. Define a new structured artifact:
   work/<spId>/25-traceability/traceability-map.json

2. The artifact must represent logical stored procedure blocks, not raw line-by-line output

3. Each block must include:

* blockId
* startLine
* endLine
* kind
* summary
* classification
* migrationIntent
* confidence
* dependencies
* transactionalImpact
* sideEffects
* risks
* notes

4. Add a top-level summary section including:

* totalBlocks
* hasUnknownDependencies
* hasTransactionalBlocks
* hasSideEffects

5. Create TypeScript interfaces for this artifact

Suggested file:
src/core/traceability-types.ts

6. Keep the artifact small, auditable and future-compatible with semantic chunking

7. Do NOT include raw stored procedure code in the JSON

8. Do NOT over-engineer with AST-level structure yet

Goal:
Create the minimal but robust contract needed for traceability discovery,
so the future design stage can consume structured block-level migration intent.
