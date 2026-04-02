# Traceability Module - Acceptance Checklist

## Functional Acceptance

* [ ] The module reads `sp-numbered.sql`
* [ ] The module reads `diagnosis.md`
* [ ] The module generates `traceability-map.json`
* [ ] The module generates `traceability-extraction-debug.json` during MVP

## Extraction Acceptance

* [ ] The module detects diagnosis sections correctly
* [ ] The module uses `## Line-by-Line Classification` as the primary extraction source
* [ ] At least one valid block is created from a valid diagnosis artifact
* [ ] Block line ranges are extracted deterministically
* [ ] Block ordering is deterministic

## Structural Acceptance

* [ ] Every block has `blockId`
* [ ] Every block has `order`
* [ ] Every block has `startLine`
* [ ] Every block has `endLine`
* [ ] Every block has `kind`
* [ ] Every block has `summary`

## Semantic Safety Acceptance

* [ ] The module does not invent semantics
* [ ] Unknown values are emitted explicitly
* [ ] The module does not encode final design decisions
* [ ] The module does not promote uncertain behavior into false certainty

## Failure Handling Acceptance

* [ ] The module fails if diagnosis is missing
* [ ] The module fails if numbered SP is missing
* [ ] The module fails if zero blocks are extracted from a valid diagnosis artifact
* [ ] The debug artifact is persisted before failing when possible

## Summary Acceptance

* [ ] `TraceabilitySummary.totalBlocks` matches `blocks.length`
* [ ] Summary counters are internally consistent
* [ ] `hasUnknownDependencies` matches actual block dependencies
* [ ] `hasTransactionalBlocks` matches actual transaction flags
* [ ] `hasSideEffects` matches actual side effect usage

## Final Acceptance

* [ ] The artifact is deterministic
* [ ] The artifact is auditable
* [ ] The artifact is usable by the design stage as a structured bridge
