Implement a deterministic traceability map builder for the migration pipeline.

Context:

* The migration pipeline already has operational stages for init, numbering, and diagnosis
* Diagnosis produces a structured diagnosis artifact from the numbered stored procedure
* The next architectural need is to build a traceability artifact that bridges diagnosis and design
* The traceability map must remain semantically faithful to diagnosis and to the numbered SP
* The traceability layer must NOT become a design layer
* It must remain deterministic, auditable, and compatible with future semantic chunking

Goal:
Create a module that builds a TraceabilityMap artifact from:

* the numbered stored procedure
* the diagnosis artifact

Target file:

* src/core/traceability-map-builder.ts

Dependencies:

* src/core/traceability-types.ts
* diagnosis artifact
* numbered stored procedure artifact

Input artifacts:

* work/<spId>/10-prepared/sp-numbered.sql
* work/<spId>/20-analysis/diagnosis.md

Output artifact:

* work/<spId>/20-analysis/traceability-map.json

Requirements:

1. Export a function:

```ts
buildTraceabilityMap(input: {
  spId: string;
  numberedStoredProcedure: string;
  diagnosisMarkdown: string;
}): TraceabilityMap
```

2. The builder must use the contract defined in:

* src/core/traceability-types.ts

3. The builder must be deterministic:

* same inputs must produce equivalent output
* no random ordering
* no speculative inference
* no LLM invocation

4. The builder must NOT:

* generate design decisions
* invent missing semantics
* create new business assumptions
* reinterpret diagnosis beyond what is explicitly stated

5. The builder must create TraceabilityBlock entries from diagnosis sections and line references.

At minimum, it must:

* detect line ranges referenced in diagnosis
* create blocks with:

  * blockId
  * order
  * startLine
  * endLine
  * kind
  * operationalSubtype when applicable
  * summary
  * confidence
  * targetLayerCandidate
  * dependencies
  * transaction
  * sideEffects
  * risks
  * notes when needed

6. Mapping rules:

A. Use diagnosis as the primary semantic source
B. Use the numbered SP as the source of truth for line boundaries
C. If a block kind cannot be confidently determined, use:
kind: 'unknown'
D. If target layer cannot be confidently determined, use:
targetLayerCandidate: 'undecided'
E. If dependency type cannot be confidently determined, use:
type: 'unknown'
F. If confidence is not explicitly inferable from diagnosis, use:
confidence: 'unknown'
G. If operational subtype does not apply, use:
operationalSubtype: 'none'

7. Classification alignment:

The builder must align block kinds with diagnosis taxonomy:

* business_logic -> kind: 'business_logic'
* operational_logic -> kind: 'operational_logic'
* data_access -> kind: 'data_access'
* side effects -> kind: 'side_effect'
* unknown_dependency -> kind: 'external_dependency' or 'unknown' depending on certainty

8. Operational subtype mapping:
   Only apply subtype when diagnosis gives enough signal:

* orchestration
* transaction_control
* iteration
* exception_flow
* declaration
* validation
  Otherwise:
* operationalSubtype: 'none'

9. Dependency extraction:
   Build structured dependencies from diagnosis when possible.
   Supported dependency types:

* table
* view
* function
* stored_procedure
* internal_block
* unknown

10. Transaction characteristics:
    Do NOT invent transaction semantics.
    Only mark:

* hasTransactionalImpact
* opensTransactionBoundary
* dependsOnCommitOrder
* dependsOnRollbackSemantics
  when diagnosis provides enough evidence.
  Otherwise default to false.

11. Block summary:
    Each block must have a concise summary derived from diagnosis content.
    Do NOT copy large diagnosis sections verbatim.

12. Summary aggregation:
    Populate TraceabilitySummary correctly:

* totalBlocks
* businessLogicBlocks
* operationalLogicBlocks
* dataAccessBlocks
* externalDependencyBlocks
* ambiguousBlocks
* highConfidenceBlocks
* mediumConfidenceBlocks
* lowConfidenceBlocks
* hasUnknownDependencies
* hasTransactionalBlocks
* hasSideEffects

13. Ordering:
    Blocks must be sorted by:

* startLine ascending
* then order ascending

14. Block IDs:
    Use a deterministic scheme such as:

* block-001
* block-002
* block-003

15. Persist artifact:
    Integrate the builder into the diagnosis pipeline so that after diagnosis succeeds,
    the pipeline also writes:

* work/<spId>/20-analysis/traceability-map.json

16. Keep the implementation minimal and production-like:

* no speculative parsing heuristics beyond what is necessary
* no LLM usage
* no future chunking logic yet
* but keep structure compatible with future chunk segmentation

17. If useful, introduce small helper functions such as:

* extractLineRanges(...)
* mapDiagnosisSectionToBlockKind(...)
* inferOperationalSubtype(...)
* inferDependencies(...)
* buildTraceabilitySummary(...)

18. Important architectural constraint:
    This builder creates a traceability artifact, not a design artifact.
    It may include targetLayerCandidate as a preliminary routing hint,
    but it must NOT encode final design commitments.

Goal:
Produce a deterministic traceability-map.json artifact that bridges diagnosis and design
and prepares the pipeline for future semantic chunking and reuse-aware code generation.
