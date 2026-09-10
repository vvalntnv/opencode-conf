## Local Harness Rules

- If the repository has a `.codegraph/` directory, use CodeGraph before grep, find, or broad file reading to locate and understand code.
- Handle a bounded, clear change touching at most three files directly when delegation would add more overhead than value. Delegate when work has independent lanes or needs specialist research, architecture judgment, UI/UX judgment, or a bounded implementation lane.
- Assign one writer per file at a time. Parallel writers must have explicit, non-overlapping file ownership.
- Use `@librarian` for current framework, SDK, and external documentation research. `@docs-explorer` is retired; do not delegate to it.
- Delegate to `@review_comments_fixer` only for explicit `ReviewComment` blocks. It owns only those edits and must report its verification result.
- After every code change, run the narrowest relevant existing test, typecheck, lint, or build command. State the exact command and result; if it cannot run, state why.
- Do not commit, push, install or upgrade dependencies, run destructive commands, or apply database migrations unless the user explicitly requests it.
