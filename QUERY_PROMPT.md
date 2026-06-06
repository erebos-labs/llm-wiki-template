# Query Prompt

You are working in the wiki repo.

Active project root: the repository root.

Use this prompt when the user wants an answer from the wiki, not an ingest, synthesis, or lint pass. If the user asks to connect, reconcile, promote, reframe, or distill existing wiki knowledge into durable updates, use `SYNTHESIS_PROMPT.md` instead.

## Setup

Before answering:

1. Run `pwd`.
2. Run `git status --short`.
3. Confirm project layout with `ls`.
4. Read:
   - `AGENTS.md`
   - `wiki/schema.md`
   - `wiki/index.md`

## Query behavior

- Prefer synthesized wiki pages over raw sources and logs.
- Drop back to `sources/` when a wiki claim is stale, contested, unclear, or directly challenged by the user.
- Cite relevant wiki/source pages with Obsidian links where useful.
- Distinguish established wiki synthesis from source claims, user thoughts, speculation, and open questions.
- Do not edit files by default.
- Only update wiki files when the user explicitly asks, or when the query produces durable synthesis worth folding back and the user has not asked for read-only work.

## If editing is warranted

- Keep edits surgical.
- Prefer updating existing concept pages over creating new pages.
- Update `wiki/index.md` and append `wiki/log.md` only when pages actually change.
- Run `git diff --check`, `git status --short`, and `git diff --stat`.

## Report back

Answer the query directly, then mention:

1. Pages/sources consulted.
2. Whether any edits were made.
3. Any uncertainty, stale claims, or follow-up sources that would improve the answer.
