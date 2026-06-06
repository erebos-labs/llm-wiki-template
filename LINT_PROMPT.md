# Lint Prompt

You are working in the wiki repo.

Active project root: the repository root.

Use this prompt for read-only health checks unless the user explicitly asks for repairs.

## Setup

Before linting:

1. Run `pwd`.
2. Run `git status --short`.
3. Confirm project layout with `ls`.
4. Read:
   - `AGENTS.md`
   - `wiki/schema.md`
   - `wiki/index.md`

## Read-only checks

Run in order:

1. **Log heading format.** Every log heading must be `## [YYYY-MM-DD HH:mm <timezone>] action | Title`. Flag any missing `HH:mm`. Do not invent timestamps.

2. **Raw filename convention.** Scan `sources/` for filenames containing spaces, mixed case, special characters, or emojis. Every raw artifact should be `{slug}-raw.md` (lowercase kebab). Flag each non-conforming file with its suggested new name. If repairs are requested: `git mv`, update the `Original artifact` field in the source summary, note log.md references as historical (append-only, skip).

3. **Index coverage.** Every `.md` file in `wiki/` (except `index.md`, `log.md`, `schema.md`, `thoughts.md`, `ingest-backlog.md`, `reading-list.md`) should appear in `wiki/index.md`. Flag missing entries.

4. **Source link integrity.** For each `[[../sources/...]]` wikilink in wiki pages (excluding `log.md`): check the target path exists. Flag broken links with the correct path. Historical log references are append-only — skip.

5. **Inter-wiki link integrity.** For each `[[page-slug]]` wikilink in wiki pages (excluding `log.md`): check `wiki/{page-slug}.md` exists. Forward references in `thoughts.md` are intentional — note them, do not create the pages.

6. **Schema and sourcing quality.** Concept pages missing a `## Tensions / Open Questions` section, summary pages with broken `**Source:**` fields, or pages with `**Type:**` mismatches against `wiki/schema.md`.

7. **Recency bias.** Flag concept pages where all sources are from the last 30 days without older foundational backing.

Do not modify files during a lint pass unless the user asks for fixes.

## If repairs are requested

- Keep fixes minimal and schema-aligned.
- Do not rewrite page substance unless the lint finding requires it.
- Update `wiki/log.md` for material repairs.
- Run `git diff --check`, `git status --short`, and `git diff --stat`.

## Report back

Lead with findings, ordered by severity:

1. Issue.
2. File/path.
3. Why it matters.
4. Suggested fix.

Then summarize checks run, files changed if any, and remaining risks.
