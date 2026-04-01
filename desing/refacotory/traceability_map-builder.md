Refactor the traceability map builder because the current implementation is structurally incomplete.

Problem:
The current traceability-map.json is being generated with:

* totalBlocks = 0
* blocks = []

This is not a valid result when diagnosis.md already exists and contains structured analysis.

This means the builder is not actually mapping diagnosis content into traceability blocks.

Goal:
Implement real deterministic extraction of traceability blocks from diagnosis.md.

Target file:

* src/core/traceability-map-builder.ts

Requirements:

1. The builder must treat:

* "## Line-by-Line Classification"
  as the PRIMARY source for block extraction.

2. It must parse classification entries that follow patterns such as:

* business_logic: lines [010-042] ...
* operational_logic: lines [043-048] ...
* data_access: lines [049-071] ...
* side_effects: lines [060-071] ...
* unknown_dependency: lines [030-034] ...

3. For each parsed entry, create a TraceabilityBlock with:

* blockId
* order
* startLine
* endLine
* kind
* summary

4. Mapping rules:

* business_logic -> kind: 'business_logic'
* operational_logic -> kind: 'operational_logic'
* data_access -> kind: 'data_access'
* side_effects -> kind: 'side_effect'
* unknown_dependency -> kind: 'external_dependency' or 'unknown' only if certainty is insufficient

5. The builder must then enrich blocks using the other diagnosis sections:

* Hidden or Implicit Rules -> confidence, notes, risks
* External Dependencies -> dependencies
* DDD-Oriented Interpretation -> targetLayerCandidate
* Migration Risks -> risks
* Uncertainty and Open Questions -> ambiguity flags and notes

6. If no blocks can be extracted from "## Line-by-Line Classification", the builder must NOT silently return an empty traceability map.
   Instead it must fail explicitly with a clear error message.

7. Add a minimal traceability validator if useful.
   At minimum validate:

* blocks.length > 0
* each block has valid line range
* each block has kind
* each block has summary

8. Update the diagnosis stage integration so traceability-map.json is only persisted when traceability extraction succeeds.

9. Keep the implementation deterministic:

* no LLM
* no speculative inference
* no fallback to empty success

10. Important:
    Do NOT redesign the contract.
    Do NOT redesign diagnosis.
    Fix the extraction logic so diagnosis is actually mapped into traceability blocks.

Goal:
Make traceability-map.json a real semantic bridge artifact, not an empty scaffold.
