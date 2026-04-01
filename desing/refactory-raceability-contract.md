Refactor the traceability contract types to align with the migration pipeline semantics defined for legacy stored procedure modernization.

Context:

* The current traceability contract is promising but semantically insufficient
* It is intended to bridge diagnosis and design stages
* Therefore it must represent:

  * semantic block discovery
  * traceability to numbered SP lines
  * preliminary architectural targeting
  * dependencies
  * transactional characteristics
  * migration risks
* It must NOT prematurely encode final design decisions
* It must remain deterministic, explicit, and reusable by future stages such as chunking, design, codegen, and review

Goal:
Refactor the existing traceability types into a more precise, architecture-aligned contract.

Target file:

* src/core/traceability-types.ts

Requirements:

1. Replace the current BlockKind with exactly this enum:

```ts
export type BlockKind =
  | 'business_rule'
  | 'orchestration'
  | 'data_access'
  | 'side_effect'
  | 'transaction_control'
  | 'external_dependency'
  | 'validation'
  | 'exception_flow'
  | 'iteration'
  | 'declaration'
  | 'unknown';
```

2. Keep ConfidenceLevel, but use exactly:

```ts
export type ConfidenceLevel = 'high' | 'medium' | 'low' | 'unknown';
```

3. Introduce TargetLayerCandidate:

```ts
export type TargetLayerCandidate =
  | 'application_service'
  | 'domain_service'
  | 'repository'
  | 'domain_model'
  | 'external_dependency'
  | 'undecided';
```

4. Replace the current dependency representation with structured dependencies:

```ts
export type DependencyType =
  | 'table'
  | 'view'
  | 'function'
  | 'stored_procedure'
  | 'internal_block'
  | 'unknown';

export interface BlockDependency {
  type: DependencyType;
  name: string;
  lines?: number[];
  knownBehavior: boolean;
  notes?: string;
}
```

5. Replace the current classification enum with explicit characteristics:

```ts
export interface BlockCharacteristics {
  deterministic: boolean;
  stateful: boolean;
  hasSideEffects: boolean;
  ambiguous: boolean;
}
```

6. Replace transactionalImpact with a structured transaction model:

```ts
export interface TransactionCharacteristics {
  hasTransactionalImpact: boolean;
  opensTransactionBoundary: boolean;
  dependsOnCommitOrder: boolean;
  dependsOnRollbackSemantics: boolean;
}
```

7. Refactor TraceabilityBlock so it uses exactly these fields:

```ts
export interface TraceabilityBlock {
  blockId: string;
  order: number;
  startLine: number;
  endLine: number;
  kind: BlockKind;
  summary: string;
  sourceSnippet?: string;
  characteristics: BlockCharacteristics;
  confidence: ConfidenceLevel;
  targetLayerCandidate: TargetLayerCandidate;
  dependencies: BlockDependency[];
  transaction: TransactionCharacteristics;
  sideEffects: string[];
  risks: string[];
  notes?: string;
}
```

8. Refactor TraceabilitySummary so it includes richer summary statistics:

```ts
export interface TraceabilitySummary {
  totalBlocks: number;
  businessRuleBlocks: number;
  dataAccessBlocks: number;
  externalDependencyBlocks: number;
  ambiguousBlocks: number;
  highConfidenceBlocks: number;
  mediumConfidenceBlocks: number;
  lowConfidenceBlocks: number;
  hasUnknownDependencies: boolean;
  hasTransactionalBlocks: boolean;
  hasSideEffects: boolean;
}
```

9. Refactor TraceabilityMap so it uses exactly:

```ts
export interface TraceabilityMap {
  version: string;
  spId: string;
  createdAt: string;
  summary: TraceabilitySummary;
  blocks: TraceabilityBlock[];
}
```

10. Remove or replace obsolete concepts that no longer fit this contract:

* the old Classification enum
* the old MigrationIntent enum from the block base contract
* the old flat string[] dependency model
* the old transactionalImpact: 'local' | 'global'

11. Keep the module focused only on type definitions.
    Do NOT add business logic, builders, validators, parsers, or runtime code in this file.

12. Preserve clarity and maintainability:

* keep comments concise and useful
* do not add speculative fields
* do not rename fields beyond what is explicitly required above

13. Important architectural constraint:
    This contract is a bridge between diagnosis and design.
    It may include preliminary architectural targeting through targetLayerCandidate,
    but it must NOT encode final design commitments.

14. Output:
    Refactor the file completely so the final result matches this specification exactly.

Do not redesign beyond the requested contract.
Do not introduce additional enums or interfaces unless required by the specification above.
