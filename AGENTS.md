# LLM Wiki Template — Repo Agent Rules (AGENTS.md)

This file is the substantive ruleset for any agent editing files in this repo — Claude Code, Codex, or others. `CLAUDE.md` is a thin pointer to this file. Keep one source of truth here.

This file covers repo agent rules — how to edit the wiki safely. Runtime wiki schema (how the wiki ingests, cites, links, queries, promotes, lints) and domain playbooks live in separate files when they exist. If a request belongs in another layer, say so and stop.

Project root: the repository root.

Do not modify files outside this folder unless explicitly instructed.

## Core Agent Rules

1. Think before editing.
   - State assumptions when they matter.
   - Surface ambiguity instead of silently choosing.
   - Push back if the requested approach is overcomplicated, unsafe, or belongs in another layer.

2. Simplicity first.
   - Prefer the smallest change that solves the actual problem.
   - Do not add speculative abstractions, frameworks, config systems, folders, templates, schemas, scripts, or automation.
   - If a manual or markdown-first solution is enough, use that first.

3. Surgical changes.
   - Touch only files needed for the task.
   - Do not refactor, reformat, or reorganize unrelated files.
   - If unrelated cleanup is noticed, mention it instead of doing it.

4. Goal-driven execution.
   - Convert vague tasks into verifiable outcomes.
   - For fixes, reproduce or inspect the issue first where practical.
   - For wiki work, verify that the edit preserves source context, index/log expectations, and the durable-vs-noisy boundary.
   - Run relevant checks when available.

5. Safety and secrets.
   - Never read, copy, print, store, suggest storing, echo back, or modify secrets, API keys, passwords, tokens, cookies, session dumps, SSH keys, browser profiles, password-manager data, raw credentials, seed phrases, private keys, or sensitive/credential-bearing logs.
   - Ask before installing global packages or changing machine-level agent behavior.
   - Do not create background daemons, launch agents, cron jobs, persistent services, network fetchers, vector databases, complex RAG, or autonomous ingestion unless explicitly requested and justified by repeated real friction.

6. Definition of Done.
   - Summarize what changed.
   - Mention files changed.
   - Mention what was checked or why checks were skipped.
   - Mention any uncertainty, follow-up risk, or dirty working tree state.

## Project-root safety

- This is a markdown-first, local-first, Obsidian-compatible LLM wiki.
- Project root: the repository root.
- Do not modify files outside this folder unless explicitly instructed.
- Before any edit: run `pwd` and `ls` to confirm path and current layout.
- Read this file before editing.
- When suggesting commands: state the shell, working directory, whether destructive, and what the command does.

## Multi-session safety

Before starting any substantive edit session, run:

```
pwd
git status --short
git diff --stat
tail -80 wiki/log.md
```

Classify any existing uncommitted changes before proceeding:

1. **Current session's own work** — continue normally.
2. **User-approved in-progress work** — proceed carefully and preserve it.
3. **Another agent or session's unfinished work** — stop. Ask the user whether to commit, stash, finish, or abandon that work before starting new edits.

Single-writer rule: only one agent should make substantive file changes at a time. A second agent may read or inspect concurrently, but must not write. The user must explicitly coordinate any split-write arrangement.

Log discipline:

- `wiki/log.md` is append-only. Run `tail -80 wiki/log.md` before appending to see recent entries and avoid out-of-order entries.
- Every new log entry must use this exact heading format:
  `## [YYYY-MM-DD HH:mm <timezone>] action | Title`
  Get the current time with: `TZ=<timezone> date +"%Y-%m-%d %H:%M"`
- Use the **actual write time** in the heading. Do not use the source/event date unless that is also the actual write date.
- If documenting work completed in an earlier session, note the earlier date in the body — do not backdate the heading.

Before ending a substantive edit session, run:

```
git diff --check
git status --short
git diff --stat
```

Report: files changed, checks passed or failed, whether the working tree is clean or still has uncommitted changes.

## Commit messages for failure-driven rule edits

When you edit a skill document (AGENTS.md, INGEST_PROMPT.md, LINT_PROMPT.md, QUERY_PROMPT.md) to fix a failure you actually observed, the commit message must name:

```
Failure:      what went wrong
Missing rule: the rule that was absent or unclear
Edit:         the change made
```

One commit per failure. Ordinary wiki content commits do not need this format.

Example:

```
fix(ingest): normalize arxiv source URLs

Failure: ingest cited the arxiv abstract page instead of the full paper.
Missing rule: no guidance on canonical source URL for arxiv papers.
Edit: added arxiv URL normalization rule to INGEST_PROMPT.md.
```

## Current layout (flat)

```
sources/   raw evidence, immutable or minimally edited
wiki/      summaries, concepts, indexes, decisions, and logs
```

Every addition must answer: **what just broke that requires this?**

Normal source records go directly under `sources/`. Subfolders under `sources/` are allowed only for large source corpora, immutable text collections, asset bundles, or mixed raw packages where flattening would reduce clarity or break references.

Runtime wiki structure, page types, source records, link conventions, index entries, log entries, tags, and filename conventions live in `wiki/schema.md`.

Reusable ingestion behavior lives in `INGEST_PROMPT.md`; runtime page conventions live in `wiki/schema.md`.

## Planned migration (future, not now)

Do not migrate silently or speculatively. Migration is its own deliberate task, triggered by explicit user instruction. The flat-start names `sources/` and `wiki/` remain valid until expansion is explicitly earned.

## File ownership

- `sources/` is immutable or minimally edited. Read from. Do not semantically rewrite content. Formatting fixes and filename normalization are fine; rewording an author's claim is not.
- Raw artifacts in `sources/` must use lowercase kebab filenames ending in `-raw.md` (e.g. `author-title-year-raw.md`). Rename non-standard filenames with `git mv` before or immediately after creating the source summary.
- `wiki/` is LLM-maintained. Edit freely, but cite source files for external claims where practical.
- `wiki/log.md` is append-only. Do not rewrite history.
- `wiki/index.md` is updated on every ingest.
- Detailed page templates, source-link rules, tags, and index/log formats live in `wiki/schema.md`.

## Page types

Do not conflate source records, summary pages, and concept pages. Follow `wiki/schema.md` for the current definitions, required fields, section order, source-link placement, and concept-page mechanics.

## Durable vs noisy

Before promoting anything to a refined knowledge page, ask: **what changed my model of the world?**

- Durable: principles, frameworks, distilled summaries, decisions, recurring patterns, theses.
- Noisy: daily news, infra updates, market commentary, raw social signal, unverified claims, speculation.

Raw material → `sources/`. Daily updates → dated entries in `wiki/log.md` (or a topic-specific log file in `wiki/` if volume justifies). Only durable synthesis is promoted to refined wiki pages.

Retrieval should prefer synthesized wiki pages over raw sources and logs unless the user explicitly asks for evidence, history, or source review.

**Drop back to sources when:**

- A wiki page's claims conflict with each other or with what the user is saying.
- The user provides new information that may supersede a wiki claim.
- The page is older than the source material it depends on and the topic is fast-moving.
- A lint pass has flagged the page as stale or contested.

The wiki is preferred, not infallible.

## Capture before ingestion

Sometimes the right first move is only to capture raw material, not to ingest it. If the user asks to save, capture, park, clip, or preserve a source/note without synthesis, put the raw or near-raw artifact in `sources/` with minimal metadata when practical, then stop. Do not create wiki summaries, update concepts, update `wiki/index.md`, or append `wiki/log.md` unless the user asks for ingestion or the capture itself creates durable wiki value.

If the user asks to capture a provisional thought, personal idea, rough thesis, or unsourced observation, append it to `wiki/thoughts.md`. Do not run the full ingest process unless the user explicitly asks to ingest or promote the thought.

## Core operations

Wiki work usually falls into ingest, query, synthesis, or lint. Keep these manual or semi-manual, discuss ingest/synthesis takeaways with the user before writing pages unless instructed otherwise, and follow `wiki/schema.md` for runtime formatting and logging conventions.

- Ingest behavior lives in `INGEST_PROMPT.md`.
- Query behavior lives in `QUERY_PROMPT.md`.
- Synthesis behavior lives in `SYNTHESIS_PROMPT.md`.
- Lint behavior lives in `LINT_PROMPT.md`.

### Lint checklist

Run these checks in order on a lint pass:

1. **Log heading format.** Headings without `HH:mm <timezone>` are malformed. Flag; do not invent timestamps.
2. **Raw filename convention.** Scan `sources/` for files whose names contain spaces, mixed case, special characters, or emojis. Each should be `{slug}-raw.md`. Rename non-conforming files with `git mv`, update the `Original artifact` field in the corresponding source summary, and note log.md references as historical (append-only, skip).
3. **Index coverage.** Every file in `wiki/` should appear in `wiki/index.md`. Flag any that are missing.
4. **Source link integrity.** Scan `[[../sources/...]]` wikilinks in wiki pages (excluding `log.md`) for paths that don't resolve to an existing file. Fix in the wiki page; historical log references are append-only.
5. **Inter-wiki link integrity.** Scan `[[page-slug]]` wikilinks in wiki pages for slugs that don't resolve to a file in `wiki/`. Forward references in `thoughts.md` are intentional — note them, don't create the pages.

## Model routing

Route by bottleneck, not prestige. The table below is the complete routing policy.

### Routing policy table

| Task class | Bottleneck | Default surface | Fallback surface | Escalate when |
|---|---|---|---|---|
| Capture / park raw note | Speed | Fast cheap model | Mid-tier | Source is sensitive or needs judgment beyond preservation |
| Formatting / filename / link cleanup | Speed | Fast cheap model | Mid-tier | Link fix implies semantic change or source conflict |
| Lint pass | Speed/editing | Mid-tier capable model | Fast cheap (flag-only) | Fix requires depth judgment |
| Routine source ingest | Speed/editing | Mid-tier capable model | Alternative mid-tier | Source is dense, philosophical, adversarial, or central to an existing concept |
| Dense synthesis / promotion decision | Depth | Frontier model | Mid-tier | Never auto-promote; always requires human review |
| Broad cross-source sweep | Breadth | Frontier model | Mid-tier | Sweep requires many-file comparison or large-context arbitration |
| Tool-heavy repo/process design | Process judgment | Agent with strong tool use | Alternative agent | Feature unavailable, or quota pressure forces handoff |

### Preflight classification

Before any substantial ingest or synthesis work, classify the task:

1. **Task class** — which row of the routing table applies?
2. **Complexity** — routine, dense, adversarial, or likely unsolvable on this surface?
3. **Privacy/sensitivity** — credentials, personal data, contested claims, or identity-bound material?
4. **Length** — short (<2k tokens), medium (2–20k), long (>20k)?
5. **Quota/availability** — which surfaces have spare quota?

Decision:

- If the current surface matches the default or fallback row, proceed.
- If a better surface is recommended but not required, state the recommendation and offer a lower-scope action on the current surface.
- If the current surface is materially unsuitable for durable synthesis, do not create or update concept pages. Capture lightly and mark for review instead.

### Promotion rule

Promotion decisions are never delegated to cheap models. A source is promoted only if it changes the model of the world, supports or challenges an existing concept, resolves an open question, or recurs enough to justify durable treatment.

## Index and log

Read `wiki/index.md` first when answering wiki queries. Treat `wiki/log.md` as append-only history. Exact index and log formats live in `wiki/schema.md`.

## Decision framework

Before adding any feature, file, folder, template, script, or abstraction, ask:

1. Does this make the wiki more useful right now?
2. Does it reduce repeated rediscovery?
3. Does it preserve source context?
4. Can it be done manually first?
5. Can it be deferred?

If (4) or (5) is honestly yes, defer. Default answer to new complexity is **not yet.**

## Automation stance

Ingestion and refactoring are manual or semi-manual. No autonomous ingest, agentic rewriting, or auto-promotion of daily updates.

## Security and privacy

Never store, suggest storing, or echo back any item in the Core rule 5 secrets list.

Be cautious with financial, medical, work-sensitive, and proprietary material. When in doubt, summarize at a safer abstraction level. Private-by-default, local-first, sync intentionally.

## Network and live-source hygiene

Distinguish provider-hosted fetch/search tools from local shell/network commands.

- If the agent uses a provider-hosted tool (e.g. Claude WebFetch/WebSearch), assume the outbound request is made from the provider's cloud infrastructure, not from your local machine.
- If the agent runs shell commands, curl, wget, or browser automation on the local machine, assume outbound requests originate from the machine where the command runs.
- For sensitive or access-restricted sources, prefer the least intrusive access method and preserve only the context needed for citation.
- Preserve source URL, access date/time, and relevant context in `sources/`.
- Do not store any item in the Core rule 5 secrets list.

## Context Window / Handoff Discipline

Agents should monitor context window usage during long sessions.

When context remaining falls near 20%, stop starting new substantive work. Instead:

1. Finish or safely pause the current edit.
2. Run `git status --short`.
3. Summarize: current goal, files read, files changed, important decisions made, unresolved questions, exact next recommended command or task.
4. Report whether the working tree is clean or dirty.
5. If edits were made, show the relevant diff summary.
6. Recommend starting a new session with the handoff summary.

Do not continue large refactors, broad linting, or multi-file edits below ~20% context remaining unless explicitly instructed.

### Session handoff summaries

When ending or pausing a substantial session, produce a copy-pasteable handoff block so a new agent session can resume without relying on chat history.

The handoff title must use this format:

`# <unique-work-topic> - Session Handoff - YYYY-MM-DD HH:mm <timezone>`

The handoff must include:

1. `Goal` — what the session was trying to accomplish.
2. `Files changed` — created, modified, deleted, moved, with paths and one-line purposes.
3. `Decisions` — durable decisions made, including anything explicitly deferred or rejected.
4. `Current state` — branch, working tree state, dirty files, and important constraints.
5. `Validation` — checks run and results. If no checks were run, say so explicitly.
6. `Next action` — the single best next step for the next agent.

Rules:

- Be concrete. Prefer file paths, commands, and decisions over narrative.
- Do not invent validation results. If a command was not run, say `not run`.
- Do not claim the working tree is clean unless `git status --short` was checked.

## arxiv source handling

When given an arxiv URL in any form, always fetch the full paper — not the abstract page.

Fetch priority: `arxiv.org/html/XXXX` → `arxiv.org/pdf/XXXX` → stop.

Full fetch rules live in `INGEST_PROMPT.md`.

## Workflow

User runs an agent (Claude Code, Codex, or similar) in this repo with Obsidian open on the same directory. Agent edits files; user reviews live in Obsidian. Git commits at meaningful checkpoints (after each ingest, after lint passes).

## Final guiding sentence

Edit this wiki as a practical, markdown-first, local-first knowledge base that compounds understanding over time. Capture safely, preserve sources, log the timeline, distill what matters, stay navigable. Compound without overengineering.
