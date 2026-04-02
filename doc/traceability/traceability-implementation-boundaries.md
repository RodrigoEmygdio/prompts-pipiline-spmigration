# Traceability Module - Implementation Boundaries

## 1. Module Scope

The Traceability module is responsible for building a deterministic `traceability-map.json` artifact from:

* numbered stored procedure
* diagnosis artifact

It exists only to structure already discovered semantics into stable migration units.

It does not perform design, code generation, or semantic invention.

---

## 2. Inputs

Required inputs:

* `work/<spId>/10-prepared/sp-numbered.sql`
* `work/<spId>/20-analysis/diagnosis.md`

Optional supporting inputs during MVP:

* `work/<spId>/20-analysis/traceability-extraction-debug.json`

---

## 3. Outputs

Required output:

* `work/<spId>/20-analysis/traceability-map.json`

Temporary MVP debug output:

* `work/<spId>/20-analysis/traceability-extraction-debug.json`

---

## 4. Source Hierarchy

The module must follow this source hierarchy:

### Primary semantic source

* diagnosis artifact

### Primary block extraction source

* `## Line-by-Line Classification`

### Final line truth source

* numbered stored procedure

### Secondary enrichment sources

* Hidden or Implicit Rules
* External Dependencies
* DDD-Oriented Interpretation
* Migration Risks
* Uncertainty and Open Questions

---

## 5. Allowed Behavior

The module may:

* parse diagnosis sections
* extract line ranges
* create semantic traceability blocks
* enrich blocks using diagnosis evidence
* emit explicit unknown values
* fail explicitly when extraction is not trustworthy

---

## 6. Forbidden Behavior

The module must not:

* call an LLM
* generate Java code
* generate architecture
* create final service or repository design
* invent business rules
* silently succeed with an empty block list
* infer semantics not grounded in diagnosis
* replace diagnosis with guessed structure

---

## 7. Success Conditions

A traceability build is successful only if:

* `traceability-map.json` is generated
* `blocks.length > 0`
* every block has:

  * `blockId`
  * `order`
  * `startLine`
  * `endLine`
  * `kind`
  * `summary`
* summary counters match the block list

---

## 8. Failure Conditions

The module must fail if:

* diagnosis artifact is missing
* numbered SP is missing
* `## Line-by-Line Classification` is missing or unusable for extraction
* zero blocks are produced from a valid diagnosis artifact
* line ranges cannot be trusted
* block list is structurally invalid

When failing, the module must still write the debug artifact when possible.

---

## 9. Fallback Rules

When information is insufficient, the module must use explicit safe fallbacks:

* `kind: "unknown"`
* `targetLayerCandidate: "undecided"`
* `confidence: "unknown"`
* dependency `type: "unknown"`
* `operationalSubtype: "none"`
* transaction flags default to `false`

The module must prefer explicit uncertainty over fabricated completeness.

---

## 10. Determinism Rules

The module must remain deterministic:

* same inputs -> equivalent outputs
* stable block ordering
* stable block IDs
* stable summary calculation
* no randomization
* no speculative branching

---

## 11. Block Ordering Rules

Blocks must be ordered by:

1. `startLine` ascending
2. `order` ascending

Block IDs must be deterministic, for example:

* `block-001`
* `block-002`
* `block-003`

---

## 12. Parse Strategy Rules

The module must treat `## Line-by-Line Classification` as the canonical extraction source.

Other sections may enrich blocks, but must not create unrelated speculative blocks.

If enrichment is not possible, the base block may still be emitted.

---

## 13. MVP Debugging Rule

During the current MVP phase, traceability extraction must remain observable.

The debug artifact must show:

* sections found
* classification section detection
* raw extracted classification entries
* parsed entries
* blocks created
* errors
* warnings

---

## 14. Final Rule

The module is not allowed to look “successful” while being semantically empty.

An empty map with a success status is always an error.
