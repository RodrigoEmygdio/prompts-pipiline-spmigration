Refactor the init stage so it becomes a real operational bootstrap stage
instead of a prompt-only placeholder.

Goal:
Make the pipeline executable from the first useful step.

Requirements:

1. The CLI must support:
   node dist/cli/index.js init <spId> <absoluteSpPath>

2. The init stage must:
   - validate that absoluteSpPath exists
   - create the workspace directory structure under work/<spId>/
   - create:
     - 00-input
     - 10-prepared
     - 20-analysis
     - 30-design
     - 50-review
     - meta
   - copy the source SP file into:
     work/<spId>/00-input/sp.sql
   - create meta/manifest.json

3. manifest.json should include:
   - spId
   - sourcePath
   - initializedAt
   - completedStages: ["init"]

4. The init stage should NOT call any LLM
5. The init stage should NOT build a prompt for now
6. Keep numbering dependent on init
7. After this refactor, numbering must consume:
   work/<spId>/00-input/sp.sql

8. Update CLI argument parsing accordingly:
   - init requires <spId> and <absoluteSpPath>
   - other stages require <stage> <spId>

Goal:
Make the pipeline operational from init onward, so numbering can be tested end-to-end.