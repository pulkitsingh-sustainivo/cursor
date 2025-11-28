## Create PR Command Handler

Trigger this file with `/create-pr 'prompt'` or `@.cursor/commands/create-pr.md` to generate a complete pull request summary and checklist for the current workspace state.

## Behavior

1. **Collect context** – Inspect git status, diff, and relevant files to understand the change.
2. **Summarize modifications** – Describe the scope, architecture impact, and any migrations or config updates.
3. **List testing evidence** – Document commands executed, test suites, and notable results.
4. **Highlight risks** – Capture known limitations, follow-up tasks, or rollout concerns.
5. **Produce PR-ready text** – Output Markdown that can be pasted directly into a PR description.

## Required Sections

- Title – short summary of the change.
- Summary – bullet list of key modifications grouped by area.
- Testing – explicit commands/results (e.g., `npm test`, `pnpm lint`).
- Risks/Mitigation – optional if no known risks.
- Checklist – repo-specific items (lint, tests, docs, migrations, etc.).

## Example Usage

```
/create-pr Finish order audit logging
```

This should gather the diffs, explain how audit events are produced, note any new modules/config, outline tests run, and provide a checklist confirming lint/tests/docs updates.

## Rules

- Follow `/docs/architecture.md` when describing changes that touch code.
- Never fabricate test results—run or clearly mark as not run.
- Reference files with backticks (e.g., `src/orders/orders.module.ts`).
- Keep the final PR template concise, scannable, and ready to paste.

