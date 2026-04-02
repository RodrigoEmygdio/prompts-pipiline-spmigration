{
  "blockId": "block-021",
  "order": 21,
  "startLine": 581,
  "endLine": 602,
  "kind": "exception_flow",
  "summary": "Central error handling with raiserror rollback and scheduler failure update",
  "classification": "stateful",
  "migrationIntent": "migrate_as_is",
  "confidence": "high",
  "dependencies": [
    "raiserror",
    "scheduler.update_task"
  ],
  "transactionalImpact": "global",
  "sideEffects": [
    "Rollback",
    "Scheduler failure status update"
  ],
  "risks": [
    "Error propagation semantics must match legacy behavior"
  ],
  "targetLayerCandidate": "application_service"
}