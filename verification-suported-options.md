Before implementing the child_process-based model adapter refactor,
inspect the OpenCode CLI interface available in this environment
(version 1.3.0) and determine the correct way to pass a prompt from a file.

Context:

The pipeline will invoke OpenCode through:

  opencode run --model <model> <prompt-input>

However:

- prompt size is large (~20k+ characters)
- passing prompt via command-line argument is unsafe
- temporary file mode must be used
- we need the correct CLI syntax supported by this OpenCode version

Task:

1. Execute or inspect:

   opencode run --help

2. Identify which input modes are supported:

Check whether OpenCode supports:

- --file <path>
- --prompt-file <path>
- --input <path>
- reading from stdin
- reading from redirected file input
- another documented mechanism

3. Determine the safest supported approach for large prompts:

Preferred priority:

A) explicit prompt file flag (example: --file)
B) stdin streaming
C) fallback safe alternative supported by CLI

4. Print the recommended invocation format for this environment:

Example expected output:

Recommended invocation syntax:

  opencode run --model <model> --file <promptFile>

or

  opencode run --model <model> < promptFile

or equivalent supported syntax

5. Do NOT modify any project files yet.

Goal:

Confirm the correct prompt input strategy supported by OpenCode CLI v1.3.0
so the next refactor step can implement the child_process adapter safely.