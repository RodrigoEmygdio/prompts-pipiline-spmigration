Add a deterministic debug artifact for traceability extraction so the diagnosis-to-traceability mapping can be inspected and verified explicitly.

Context:

- The current traceability extraction logic is being fixed
- This stage is still immature and requires strong observability
- We need a debug artifact that shows exactly what the builder found and how it transformed diagnosis into traceability blocks
- This debug artifact is temporary but important during the current evolution phase
- It must be deterministic, compact, and useful for developers and reviewers

Goal:
Generate a traceability extraction debug artifact during traceability map building.

Target output file:

- work/<spId>/20-analysis/traceability-extraction-debug.json

Requirements:

1. The debug artifact must be written during traceability map generation.

2. The artifact must include at least these top-level fields:

{
  "spId": "...",
  "generatedAt": "...",
  "sectionsFound": [],
  "classificationSectionFound": true,
  "classificationEntriesRaw": [],
  "classificationEntriesParsed": [],
  "blockEnrichmentSources": {},
  "blocksCreated": [],
  "errors": [],
  "warnings": []
}

3. "sectionsFound" must list the diagnosis sections detected, for example:

- Functional Summary
- Line-by-Line Classification
- Hidden or Implicit Rules
- External Dependencies
- DDD-Oriented Interpretation
- Migration Risks
- Uncertainty and Open Questions

4. "classificationSectionFound" must explicitly indicate whether:

- "## Line-by-Line Classification"
  was found

5. "classificationEntriesRaw" must contain the raw classification lines or items extracted from the diagnosis classification section

6. "classificationEntriesParsed" must contain structured parsed results before block creation, for example:

{
  "raw": "business_logic: lines [010-042] validate and compute event risk status",
  "kind": "business_logic",
  "startLine": 10,
  "endLine": 42,
  "summary": "validate and compute event risk status",
  "parseSuccess": true
}

7. If parsing fails for an entry, record it explicitly:

{
  "raw": "...",
  "parseSuccess": false,
  "error": "Could not extract line range"
}

8. "blockEnrichmentSources" must summarize what secondary diagnosis sections were used to enrich blocks, for example:

{
  "hiddenRulesUsed": true,
  "externalDependenciesUsed": true,
  "dddInterpretationUsed": true,
  "migrationRisksUsed": true,
  "uncertaintyUsed": true
}

9. "blocksCreated" must contain a compact per-block summary after construction, for example:

{
  "blockId": "block-001",
  "startLine": 10,
  "endLine": 42,
  "kind": "business_logic",
  "targetLayerCandidate": "domain_service",
  "confidence": "medium",
  "dependencyCount": 1,
  "riskCount": 2,
  "sideEffectCount": 0
}

10. "errors" must contain fatal extraction problems
    Examples:

- classification section not found
- zero blocks created
- invalid line range

11. "warnings" must contain non-fatal issues
    Examples:

- ambiguous dependency type
- missing target layer candidate
- confidence defaulted to unknown

12. Important behavior:

- if traceability extraction fails, still persist the debug artifact before failing the stage
- do NOT hide failures
- do NOT silently return an empty traceability map without corresponding debug evidence

13. Keep the artifact compact:

- do not dump the full diagnosis
- do not dump the full SP
- include only evidence useful for debugging extraction logic

14. Do NOT redesign the traceability contract
15. Do NOT redesign diagnosis
16. Only add deterministic observability for extraction

Goal:
Make traceability extraction inspectable and debuggable so failures are obvious,
localized, and actionable during the current MVP evolution phase.