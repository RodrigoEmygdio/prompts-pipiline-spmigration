Adapt the existing codebase to the refactored traceability contract without changing the architectural intent of the pipeline.

Context:

* The traceability contract in src/core/traceability-types.ts has been refactored
* Existing code may still depend on the old contract
* The goal now is to update the dependent code safely and deterministically
* This adaptation must be minimal, explicit, and non-speculative
* Do NOT redesign the pipeline
* Do NOT add new behavior unless strictly required to make the code compile and preserve the existing intent

Goal:
Refactor all direct usages of the old traceability contract so they are compatible with the new contract.

Scope:

* Search the project for every usage of the old traceability types
* Update only the affected code paths
* Keep behavior consistent with the current pipeline intent

Requirements:

1. Find all references to the old traceability contract, including usages of:

* old BlockKind values
* old Classification enum
* old MigrationIntent enum
* old flat dependencies string[]
* old transactionalImpact field

2. Update code to use the new contract exactly as defined in src/core/traceability-types.ts.

3. If any code previously depended on the old Classification enum, replace it with the new:

* characteristics.deterministic
* characteristics.stateful
* characteristics.hasSideEffects
* characteristics.ambiguous

4. If any code previously depended on MigrationIntent in TraceabilityBlock, remove that dependency from the traceability layer.
   Do NOT invent a replacement unless a compile/runtime dependency forces it.
   If forced, replace it with a minimal TODO-compatible adaptation and clearly comment it.

5. If any code previously used dependencies: string[], adapt it to use:

```ts
BlockDependency[]
```

Use structured dependency objects with:

* type
* name
* knownBehavior
  and optional:
* lines
* notes

6. If any code previously used transactionalImpact, adapt it to the new:

```ts
transaction: TransactionCharacteristics
```

Use explicit fields:

* hasTransactionalImpact
* opensTransactionBoundary
* dependsOnCommitOrder
* dependsOnRollbackSemantics

7. If any code constructs TraceabilityBlock objects, ensure all required fields are populated:

* blockId
* order
* startLine
* endLine
* kind
* summary
* characteristics
* confidence
* targetLayerCandidate
* dependencies
* transaction
* sideEffects
* risks

8. For targetLayerCandidate, use only safe preliminary values.
   If the source code does not yet know the target, use:

```ts
'undecided'
```

9. For sourceSnippet and notes, keep them optional.
   Do not force artificial values.

10. For summary counters in TraceabilitySummary, update all producers so the new fields are populated correctly:

* totalBlocks
* businessRuleBlocks
* dataAccessBlocks
* externalDependencyBlocks
* ambiguousBlocks
* highConfidenceBlocks
* mediumConfidenceBlocks
* lowConfidenceBlocks
* hasUnknownDependencies
* hasTransactionalBlocks
* hasSideEffects

11. Important constraint:
    Do NOT silently guess business semantics.
    If some producer code cannot infer a precise value yet, use the safest explicit fallback and keep the behavior deterministic.

Safe fallback rules:

* targetLayerCandidate -> 'undecided'
* dependency type -> 'unknown'
* confidence -> 'unknown'
* characteristics.ambiguous -> true when uncertain
* transaction flags -> false unless explicitly known

12. Do NOT refactor unrelated modules.
    Only touch the files that are necessary to restore compatibility with the new traceability contract.

13. Do NOT change prompt files, validators, or stage orchestration unless they directly depend on the old traceability contract.

14. After refactoring, ensure:

* TypeScript compiles
* imports are clean
* no stale references to removed enums remain
* no stale fields remain in object construction

15. Output expectations:

* minimal, precise code changes
* no speculative redesign
* preserve current pipeline intent
* adapt only what is required for compatibility
