# Diagnosis Patch Request

## Metadata
- stage: diagnosis
- spId: sched_event_risk_p
- generatedAt: 2026-03-31T10:20:00.000Z
- repairMode: patch
- failureType: missing_section
- targetSection: ## Line-by-Line Classification
- expectedAction: insert_section

## Validation Summary
- totalErrors: 2
- patchableErrors: 2
- nonPatchableErrors: 0

## Validation Errors
- Missing required section: ## Line-by-Line Classification
- Missing required section: ## DDD-Oriented Interpretation

## Patch Instructions
You must generate a minimal patch for a diagnosis artifact.

Rules:
- Use the numbered stored procedure as the source of truth
- Generate ONLY the missing or corrected section
- Do NOT rewrite the full diagnosis
- Do NOT generate code
- Do NOT add new business assumptions
- Preserve ambiguity where the code is ambiguous

Return the patch in the required patch format only.

## Existing Diagnosis Excerpt
<optional excerpt of relevant diagnosis content>

## Numbered Stored Procedure Excerpt
<relevant excerpt or full SP if needed>
