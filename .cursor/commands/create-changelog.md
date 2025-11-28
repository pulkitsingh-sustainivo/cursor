# Create Changelog Command Handler

Invoke with `/create-changelog 'summary'` or `@.cursor/commands/create-changelog.md` to snapshot the current workspace changes into a structured changelog entry.

## Behavior

1. **Inspect git state** – Read `git status`, staged/uncommitted diffs, and recent commits to understand what changed.
2. **Summarize edits** – Capture the essence of each modification (feature, bug fix, refactor) in short bullet points.
3. **Collect author metadata** – Retrieve `git config user.name` and `git config user.email`.
4. **Timestamp** – Record the current date/time in ISO 8601 (`YYYY-MM-DDTHH:mm:ssZ`).
5. **Persist changelog** – Write a Markdown file inside the `changelog/` directory following the required naming convention.

## File Naming

`<id>-<change>-<feature-name>-<username>.changelog.md`

- `id`: incremental or timestamp-based identifier (e.g., `20241128-001`).
- `change`: short action keyword (`add`, `fix`, `chore`, etc.).
- `feature-name`: kebab-case description (`order-audit`, `auth-refresh-tokens`).
- `username`: git username in kebab-case (strip spaces/special characters).

## File Content

Each changelog file must include:

- Title line with the same identifier.
- Author section listing git username and email.
- Timestamp in ISO 8601.
- Summary bullets of the detected changes.
- Optional notes for follow-ups or blockers.

## Example Usage

```
/create-changelog Added audit logging for orders
```

This should detect the related code edits, produce bullets like `- add: order audit events persisted via repository`, and emit a file such as `20241128-add-order-audit-jdoe.changelog.md`.

## Rules

- Always read `/docs/architecture.md` before describing code-level changes.
- Do not fabricate changes; only include what exists in the working tree.
- Keep descriptions concise (one line per change).
- Ensure the `changelog/` folder exists; create it if missing.
- Never overwrite an existing changelog file—use a new ID.
