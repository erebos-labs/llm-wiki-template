# Ingest Prompt

You are working in the wiki repo.

Active project root: the repository root.

You are the implementer. Follow existing wiki rules and make only justified edits.

## Setup

Before editing:

1. Run `pwd`.
2. Run `git status --short`.
3. Confirm project layout with `ls`.
4. Read:
   - `AGENTS.md`
   - `wiki/schema.md`

Before performing any live web fetch or search, read the **Network and live-source hygiene** section in `AGENTS.md`.

## Scope

Do not modify files outside the repository root.

Do not create new folders, automation, templates, scripts, or domain playbooks unless explicitly asked.

Do not commit unless explicitly asked.

## arxiv source handling

When given an arxiv URL in any form (`arxiv.org/abs/XXXX`, `arxiv.org/XXXX`, or bare paper ID), always fetch the full paper — not the abstract page:

Fetch priority: `arxiv.org/html/XXXX` → `arxiv.org/pdf/XXXX` → stop.

1. Try `https://arxiv.org/html/XXXX` first. If available, fetch and convert to markdown with `pandoc` when available. Preferred conversion: `pandoc -f html -t gfm --wrap=none <html-file> -o <raw-md-file>`. Preserve source URL and capture method in the raw artifact or source record. If `pandoc` fails, fall back to a simpler local text conversion and note the fallback in the source record.
2. If HTML fails, fall back to `https://arxiv.org/pdf/XXXX`. Read in page-range chunks and convert to markdown. Note in the source record that the PDF was used.
3. If both fail and only the abstract is available, do not proceed — inform the user and wait for instruction.

Do not use the `/abs/` URL as a source. Do not rely on web clippings or user-pasted abstracts when the full paper is fetchable.

## Task

Ingest the source or sources specified by the user into the wiki using the existing flat layout and schema.

## Backlog Reconciliation

At the start of an ingest, check `wiki/ingest-backlog.md` if it exists. If the source or idea being ingested matches a backlog item, treat that item as addressed.

At the end of a successful ingest:
- Remove the backlog item if fully addressed.
- Rewrite it to only the remaining unresolved work if partially addressed.
- Note resolved backlog items in the `wiki/log.md` entry where applicable.

Do not maintain a completed-backlog section. The log is the historical record; the backlog should contain only pending work.

## Capture escape hatch

If the user only asks to save, capture, park, clip, or preserve raw material, do a lightweight capture: preserve the raw or near-raw artifact in `sources/` with minimal provenance when practical, then report where it was saved. Do not create a source record, summary page, concept update, index entry, or log entry unless the user asks for ingestion or the capture itself clearly needs wiki-facing metadata.

## Ingest preflight

Before writing durable wiki content:

1. Read AGENTS.md.
2. Inspect the source lightly.
3. Classify:
   - source type
   - complexity
   - length
   - likely treatment: source-only / summary / concept update / new concept / log-only
   - recommended model, if current model is not ideal
4. If current model is adequate, proceed.
5. If current model is not ideal, say what can safely be done now: capture only, rough summary only, defer concept synthesis, or proceed with caveat.

## Batch ingest context strategy

When the user presents multiple sources to ingest in one session, decide the session strategy **before writing any files**. State the decision in one sentence, then proceed without asking for confirmation.

### Step 1: Read all pending sources first

Before creating any source records or wiki pages, read every source file queued for ingest. For each one note:

- **Line count / token volume** — rough proxy for context cost
- **Noise level** — embedded SVGs, LaTeX math, large HTML tables, raw appendices add context cost without synthesis value
- **Relevance tier** — high (concept page or significant foldback), medium (source record + summary), low (capture-only)

### Step 2: Choose a strategy

**Auto-compaction — proceed in one session (default):**
- All sources combined are under ~1,500 lines of clean prose, or
- No single source exceeds ~500 lines with heavy noise

**Read the large source in full first, then proceed in one session:**
- One source is large (500–1,000+ lines) or noisy (embedded SVGs, math, large appendices)
- Read all unread sections of that source before starting any ingest work, then ingest all sources in the same session

**Split into separate sessions — tell the user:**
- Multiple sources are large and noisy (combined clean prose > ~2,500 lines), or one source alone exceeds ~1,500 lines of dense prose
- Ingest the smaller/higher-priority sources in the current session, produce a handoff block, and flag what remains

### Step 3: Ingest order within a session

Order by priority:

1. Breaking news / time-sensitive reports
2. GitHub READMEs and tool releases
3. Engineering blog posts and threads
4. Academic papers
5. Low-relevance captures (capture-only treatment, skip full ingest)

## Source-only ingest

Use source-only ingest when a source is worth preserving but does not yet change a durable wiki thesis.

- Create a dated source file in `sources/`.
- Add a metadata header with type, source kind, status, disposition, and ingested date.
- Add an `## Ingest judgment` section briefly stating whether any durable page update was considered.
- Do not update `wiki/log.md` or `wiki/index.md` unless a durable wiki page is created or changed.

## Raw artifact filename normalization

Before creating the source summary, rename any raw artifact with a non-standard name to `{slug}-raw.md` (lowercase kebab + `-raw` suffix). Update the `Original artifact` field in the source summary to reflect the new name.

Source summaries use the suffix `-source-summary.md`.

Before writing wiki pages, classify each source as one of:

- Primary source / raw text / legal / scripture / transcript
- Historical or factual reference
- Argument / essay / thesis / opinion
- Technical paper / report / documentation
- News / daily update / market commentary / timeline archive
- Project decision / internal note

For each source:

1. Preserve source context.
2. Create or update the canonical Markdown source record in `sources/`. Create normal source records directly under `sources/`.
3. When practical, preserve the raw or near-raw source separately in `sources/`. Keep the `.md` source record canonical, pointing to both the original URL and the local raw copy.
4. Create or update a source-specific summary page in `wiki/` when the source type warrants a durable summary.
5. Update `wiki/index.md` for any new or changed wiki page.
6. Append an entry to `wiki/log.md`.

## Source-aware behavior

For primary sources, raw texts, legal texts, and transcripts:
- Preserve the source as evidence. Do not normalize, translate, summarize, or split immutable texts unless explicitly requested.

For historical or factual references:
- Create a concise source-specific summary page when the source adds durable reference value.

For arguments, essays, theses, opinions, technical papers, reports, and documentation:
- Create or update a source-specific summary page when useful.
- Fold back durable patterns into existing concept pages when they strengthen or change reusable knowledge.

For news, daily updates, and market commentary:
- Treat as chronological evidence, not durable knowledge by default.
- Preserve in `sources/`. Add a concise dated entry to `wiki/log.md`.
- Extract recurring patterns only if useful beyond the day's update.
- Create a new concept page only when the source contributes a durable pattern or synthesis worth reusing.
- Do not create one wiki page per headline.

For podcasts, interviews, and videos:

**Transcript gate:** Before ingesting any podcast, video, or audio source, verify whether a full transcript is available.
- **Full transcript available:** proceed normally.
- **Full transcript not available:** stop and check with the user. Options: (a) abort, (b) proceed with summary-only capture. If the user approves a summary, label the source record and any wiki page `[SUMMARY — no transcript available]`, and treat all extracted content as lower-confidence paraphrase.
- **Partial transcript:** treat as no transcript unless missing portions are brief and non-central.

Transcript sources in order of preference: official published transcript → auto-captions (yt-dlp) → third-party transcript → manual extraction. Record provenance in the source record.

- Attribute interview claims: "X argues...", "Y frames...", "The interview presents..."
- Do not promote podcast/interview claims into concept pages merely because the speaker is high-status.
- Promote only when the source changes, strengthens, contradicts, or usefully complicates an existing model.

## Source Discovery From Interviews

When ingesting a podcast, interview, or video, optionally add a `Potential Follow-up Sources` section. Group suggested sources by topic and label by purpose: support, challenge, primary source, background, empirical evidence, opposing view. Do not automatically ingest suggested sources.

## Fiction / Cultural Sources

May be ingested as material for durable concepts, symbols, patterns, and reusable vocabulary. Must not become a fan wiki, lore archive, or plot database.

Avoid exhaustive plot summaries, character database entries, lore completionism, or flattening culturally overloaded works into a single interpretation. Preserve uncertainty where interpretation is contested.

Distinguish: (1) what the source says, (2) what the fictional work depicts, (3) what the wiki infers or synthesizes.

## Concept-page rules

Do not create a new concept page unless the source clearly earns one. Prefer updating existing concept pages when the source changes or strengthens an existing durable idea.

A concept page is earned only if there is real synthesis, not just one interesting source.

## Promotion test

Before updating or creating durable wiki concepts, ask: **What changed my model of the world?**

Only promote durable lessons, frameworks, contradictions, recurring patterns, theses, or decisions. Do not promote noisy facts, daily news, speculation, raw social signal, or one-off details.

## Reusable idea durability

When updating a reusable concept page, avoid concept inflation:

- **Add a new entry** only when the current source explicitly supports the mechanism and the idea has clear future retrieval value.
- **Mark provisional** when an entry is promising but supported by only one clean source: add `**[provisional]** — one explicit source; promote after [X].`
- **Promote from provisional to durable** only when a second ingested source explicitly confirms the mechanism.
- **Do not promote by analogy.** A plausible second case that has not been ingested does not count toward the durable threshold.

## Blind-spot check

When ingesting a source or updating a concept page, briefly consider:

- What perspective does this source represent?
- What perspective is missing?
- What would change my mind?
- Are there credible opposing sources worth adding?

## Log entry

When appending to `wiki/log.md`:

1. Run `tail -80 wiki/log.md` first to see recent entries and avoid out-of-order appends.
2. Get the current time in your configured timezone:
   ```
   TZ=<timezone> date +"%Y-%m-%d %H:%M"
   ```
3. Use this exact heading format:
   ```
   ## [YYYY-MM-DD HH:mm <timezone>] action | Title
   ```
4. Use the **actual write time** in the heading. Do not use the source publication date.
5. Append one complete entry at the bottom of `wiki/log.md`. Do not rewrite old entries.
6. After appending, verify the latest entry with `tail` or a targeted grep.

## After editing

Run:

```bash
git diff --check
git status --short
git diff --stat
```

Then report back:

1. Files changed.
2. Source records created or updated.
3. Summary pages created or updated.
4. Concept pages updated, if any, and why.
5. Whether any new concept page was created, and why it was justified.
6. Whether `wiki/index.md` was updated.
7. Whether `wiki/log.md` was updated.
8. Whether `git diff --check` passed.
9. Any schema/rule issues encountered.
10. Recommended commit message.
