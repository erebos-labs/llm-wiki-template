# Wiki Schema

Describes conventions for wiki pages, source records, index entries, and log format.

---

## Source Record (`sources/`)

Every ingested source must have a Markdown source record in `sources/`. When practical, preserve the raw or near-raw source separately in `sources/` as a PDF, raw Markdown, HTML, text, transcript, or other original artifact. Wiki pages link to the Markdown source record as the canonical wiki-facing source node.

Raw evidence and source records are distinct. Raw or near-raw evidence belongs in `sources/`; the source record is a separate Markdown file containing metadata, extracted notes, relevance, cautions, and links to affected wiki pages. Do not dump raw external sources into `wiki/`.

```
# Source: <title>

**Type:** Source
**Source kind:** <Paper / Report / Blog / Vendor guidance / Other>
**Original artifact:** [[<raw-artifact-filename-if-any>]]
**Status:** Ingested
**Ingested:** <YYYY-MM-DD>
**Authors / Organization:** <known value or TODO>
**Published:** <known value or TODO>
**URL / DOI:** <known value or TODO>

## Why this source matters
<Brief note explaining why it belongs in the wiki.>

## Extracted / Relevant Content
<Brief extracted notes or key evidence. Do not duplicate the wiki summary.>

## Links

- Summary: [[<summary-page-if-exists>]]
- Concepts:
  - [[<concept-page-if-relevant>]]
```

---

## Summary Page (`wiki/`)

One source. Mirrors the source's structure and claims with attribution. LLM-maintained.

```
# <Title>

**Type:** Summary
**Source:** [[../sources/<source-summary-filename-without-.md>]]
**Authors:** <if applicable>
**Ingested:** <YYYY-MM-DD>
**Tags:** <comma-separated lowercase kebab tags>

---

## <Opening section>
<1-3 sentence source-aware summary, claim, context, decision, or event note.>

## <Section>
<Content. Use tables, bullet lists where they aid scanning.>

## Key Terminology
<Definitions of non-obvious terms introduced by this source>

## Open Questions / Research Gaps
<What the source leaves unresolved; useful for lint passes>

## Related wiki pages
<Wikilinks to related wiki pages; updated as new pages are added>
```

A summary page stays a summary until it generalizes beyond its source — extracting a framework, formalizing a model, or producing reusable principles.

---

## Blind-spot check

When ingesting a source or updating a concept page, briefly consider:

- What perspective does this source represent?
- What perspective is missing?
- What would change my mind?
- Are there credible opposing sources worth adding?

For concept pages, use `## Tensions / Open Questions` to record missing perspectives, unresolved disagreements, weak evidence, or credible opposing views. If a concept page has no meaningful tension, objection, or open question, treat that as suspicious — the page may be underdeveloped. Do not invent disagreement; only note real gaps.

---

## Recency Bias Guardrail

Newer sources should update the wiki, but they do not automatically outrank older sources.

Distinguish between:
- genuinely newer evidence that supersedes an older claim
- a newer example that merely illustrates an existing principle
- temporary news, hype, or discourse momentum
- one author's newer opinion that does not erase older stronger evidence
- durable principles that remain valid despite newer developments

Before changing a durable concept page because of a recent source, ask:

1. Is the new source higher quality, more direct, or more current for this specific claim?
2. Does it contradict the older claim, refine it, or merely add a recent example?
3. Is this a durable update or just current noise?
4. Should the new material stay in a dated log or source summary first?

Default: preserve older durable synthesis unless newer evidence clearly improves, corrects, or reframes it.

---

## Opening Section Rule

Choose the opening section based on page/source type:

- Argument / essay / thesis / opinion / analysis: `## Core Claim`
- Historical event / biography / factual reference: `## Summary`
- Primary source / scripture / legal text / transcript: `## Source Context`
- Technical paper / report / documentation: `## Main Point`
- Decision or internal project note: `## Decision`
- Market/news/log-style material: `## What Happened`

When uncertain, default to `## Summary`.

---

## News and Timeline Sources

Preserve source context, log the ingest, and promote only durable repeated patterns, frameworks, contradictions, or reusable synthesis into wiki pages. Do not create one wiki page per headline.

---

## Video / Podcast Capture Levels

### Light capture

Use when the source is interesting but not central.

Required: URL, title, speaker/host, publish date if known, capture date, capture type, reason saved, key timestamped points where available.

### Transcript capture

Use when the source is likely to support durable synthesis or direct quotation.

Required: full or near-full transcript in `sources/`, timestamps preserved where practical, transcript provenance noted.

### Promotion rule

Do not promote podcast/interview claims into concept pages merely because the speaker is high-status. Keep interview claims attributed: "X argues...", "Y frames...", "The interview presents..."

---

## Concept Page (`wiki/`)

Two or more sources, or a single source distilled into genuinely reusable knowledge. Synthesizes across sources. LLM-maintained.

```
# <Title>

**Type:** Concept
**Created:** <YYYY-MM-DD>
**Tags:** <comma-separated lowercase kebab tags>

## <Opening section>
<1-3 sentence source-aware summary, claim, or thesis.>

## <Section>
<Content. Use tables, bullet lists where they aid scanning. Cite sources per claim where practical.>

## Tensions / Open Questions

### <Named tension>
<Verbatim quote or precise paraphrase from source A.> ([source A](link))
<Verbatim quote or precise paraphrase from source B.> ([source B](link))
<One-sentence synthesis of the contradiction.>

### Open Questions
<Unresolved questions not addressed by any contributing source.>

## Related wiki pages
<Wikilinks to related wiki pages; updated as new pages are added>

## Sources

- [[../sources/<source-summary-filename-without-.md>]] — one-line role: what this source contributes
- [[../sources/<source-summary-filename-without-.md>]] — one-line role
```

**Sources list rules:**
- List only sources that contribute substantive claims to this concept page
- Links go to canonical Markdown source records in `../sources/`
- Links to wiki summaries and concept pages go under `## Related wiki pages`, not `## Sources`

**Tensions section rules:**
- Each tension is a named subsection (`###`)
- Use verbatim quotes from source pages as anchors where possible, with citations
- Do not paraphrase away the contradiction

---

## Query Operation

Queries retrieve, connect, and test existing wiki knowledge. Read `wiki/index.md` first, prefer durable wiki pages over raw sources and logs, and only edit files when the query produces durable synthesis worth folding back.

---

## Synthesis Operation

Synthesis connects, reconciles, promotes, or reframes existing wiki knowledge across multiple pages, source summaries, or logs. Use `SYNTHESIS_PROMPT.md` when the user asks for a cross-page thesis, contradiction resolution, concept promotion, durable reframing, or synthesis pass.

Synthesis defaults:
- Prefer updating existing concept pages over creating new ones.
- Preserve evidence boundaries: source claim, wiki synthesis, user thought, inference, and open question.
- Use `synthesis` or `concept` in `wiki/log.md` depending on whether the work primarily connects existing pages or updates a durable concept.

### Random Review

Random Review is a semi-random review mode where the agent chooses one existing wiki page from `wiki/index.md` and turns it into a short active-learning session.

User command: `Run Random Review.`

Selection rule:
- Prefer durable wiki pages over noisy log entries.
- Prefer concept pages, synthesis pages, linked pages, or neglected pages.
- Avoid creating new files during Random Review unless the session produces durable synthesis.

Output format:

1. Selected page
2. Why this page is worth revisiting
3. Five-bullet refresher
4. Three recall questions
5. Two connection questions linking it to other wiki pages
6. One suggested follow-up query
7. Foldback recommendation: whether anything from this session should update or create a wiki page

---

## Index Entry Format (`wiki/index.md`)

One line per page:
```
- [Page Title](page-filename.md) — **summary** or **concept** — one-line description | tags: tag1, tag2
```

Organized under category headings. Categories emerge from ingests; don't pre-create them.

---

## Log Entry Format (`wiki/log.md`)

`wiki/log.md` is append-only. New entries go at the bottom of the file.

```
## [YYYY-MM-DD HH:mm <timezone>] <op> | <title>
<optional one-line note if non-obvious>
```

`<op>` is one of: `ingest`, `query`, `lint`, `init`, `schema`, `refactor`, `capture`, `concept`, `thought`, `synthesis`, `system`.

---

## Filename Conventions

All filenames: lowercase kebab-case. No spaces, no mixed case, no special characters.

- **Raw artifacts** (web clippings, captured pages, transcripts): `{slug}-raw.md`
- **Source summaries** (metadata + extracted notes + links): `{slug}-source-summary.md`
- **Wiki summary pages**: short topic slug derived from the source title
- **Concept pages**: clean topic slug (not author + topic)
- When a concept page is created for a topic that already has a summary page with the clean slug, rename the summary to include the author or year: `topic-author-year.md`

---

## Tags

Use lowercase kebab. Prefer specific over generic.

**Rules:**
- No acronym-only tags: use `reinforcement-learning`, not `rl`
- Canonical resolution for agent-related tags: use `llm-agents`, not `agents`
- `reasoning` = the capability (chain-of-thought, inference); `reasoning-models` = the architecture class
