# Synthesis Prompt

You are working in the wiki repo.

Active project root: the repository root.

Use this prompt when the user asks to connect, reconcile, promote, reframe, or distill existing wiki knowledge across multiple pages or sources. Synthesis is distinct from ingest: it does not require a new source entering the wiki. It is also distinct from ordinary query: the expected output may be a durable wiki update, not only an answer.

## Setup

Before synthesizing:

1. Run `pwd`.
2. Run `git status --short`.
3. Confirm project layout with `ls`.
4. Read:
   - `AGENTS.md`
   - `wiki/schema.md`
   - `wiki/index.md`
5. Read the relevant concept pages, summary pages, source summaries, and recent `wiki/log.md` entries needed for the synthesis.

## Synthesis behavior

- Prefer existing concept pages over creating new ones.
- Create a new concept page only when the synthesis is durable, reusable, and not a better fit as a section in an existing page.
- Preserve source boundaries. Distinguish source claims, existing wiki synthesis, user thoughts, your inference, and open questions.
- Resolve contradictions explicitly. Do not smooth over real disagreement just to make a cleaner thesis.
- Keep durable synthesis separate from noisy news, speculation, raw social signal, or one-off product claims.
- Treat user-originated reframings as thoughts or synthesis, not external evidence.
- If the current model is materially unsuitable for the depth or breadth required, capture the synthesis question and mark it for later review instead of updating durable concept pages.

## Synthesis outputs

When editing is warranted:

- Update the smallest useful set of existing pages.
- Add or revise `## Tensions / Open Questions` when the synthesis reveals uncertainty, weak evidence, or credible counterarguments.
- Update `wiki/index.md` if a page is created or its index description materially changes.
- Append `wiki/log.md` with `synthesis` or `concept` as the operation, depending on the work.
- Do not create source summaries unless a source is actually being ingested.

## Verification

Before finishing after edits:

1. Run `git diff --check`.
2. Run `git status --short`.
3. Run `git diff --stat`.

## Report back

Answer with:

1. The synthesis made or rejected.
2. Pages/sources consulted.
3. Files changed.
4. Checks run.
5. Remaining uncertainty or follow-up sources that would strengthen the synthesis.
