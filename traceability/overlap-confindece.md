Refactor the traceability-map builder so block confidence is derived from Hidden or Implicit Rules using line-range overlap instead of direct matching.

Problem:

Traceability blocks are extracted from:

## Line-by-Line Classification

Confidence values exist in:

## Hidden or Implicit Rules

These sections do not share identical line ranges.

Therefore confidence cannot be assigned using direct range equality.

Confidence must instead be derived using line-range intersection.

Goal:

Implement deterministic confidence enrichment based on overlap between classification blocks and implicit rule ranges.

Requirements:

1. Extract implicit rules from:

## Hidden or Implicit Rules

Each rule contains:

* rule description
* line range
* confidence level

Example:

rule:
lines: [220–224]
confidence: MEDIUM

2. Extract classification blocks from:

## Line-by-Line Classification

Each classification entry contains:

* startLine
* endLine

3. Implement overlap detection:

Two ranges overlap if:

block.startLine <= rule.endLine
AND
rule.startLine <= block.endLine

4. For each classification block:

Find all implicit rules that overlap with the block range.

5. Assign confidence using priority order:

HIGH > MEDIUM > LOW

Example:

If overlapping rules include:

LOW
MEDIUM
HIGH

Then:

block.confidence = HIGH

6. If no overlapping implicit rules exist:

block.confidence = "unknown"

7. Do NOT guess confidence.

Do NOT infer confidence from classification category.

Confidence must come only from implicit rule evidence.

8. Implement this logic as a deterministic enrichment step:

classification extraction
→ implicit rule extraction
→ overlap matching
→ confidence assignment

9. Keep the implementation deterministic.

Same diagnosis input must produce same confidence results.

10. Add debug artifact support:

traceability-extraction-debug.json

Include:

implicitRulesParsed
confidenceAssignments

Goal:

Make confidence assignment evidence-based using line-range intersection rather than fragile equality matching.
