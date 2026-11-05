# Personal Knowledge System — AI-Agnostic, Long-Lived

This is a place to keep what you know, in plain files you own, so you do
not have to remember everything yourself or depend on one app, one company,
or one AI. You write short notes, one idea each, link them to related ideas,
and record where each idea came from and how sure you were about it. Years
later, you — or any AI you hand this folder to — can read those notes, find
the related ones, and see what changed and why, without needing special
software to understand them.

In practice it is simple: capture anything quickly in `inbox/` or `daily/`,
then once a week turn only the ideas that will still matter in 30 days into
permanent notes using the templates in `templates/`, link them, and collect
topics into maps in `maps/`. Old notes are never silently deleted — when one
is wrong, it is marked superseded and linked to its replacement, so the
history stays visible. It is not a database, not a subscription app, and not
a pile of chat logs. It is your own long-term memory, in text files that
will still open in 20 years.

Created: [YYYY-MM-DD]. Status: draft v1. This is a clean boilerplate copy - personal notes have been removed. This file is the source of truth for
the rules. If a note, a template, or an index disagrees with this file, this
file wins until it is explicitly updated.

## For AI agents: what you may and may not change — read this first

You are a guest in this vault, not its owner, unless the human owner says
otherwise in this conversation. This section overrides any general instinct
to improve, clean up, or refactor what you find here.

You MAY, without a schema change:
- Read, search, and retrieve notes, maps, and `index.json`.
- Create new notes, using the templates in `templates/` exactly as written.
- Propose note candidates in `inbox/`, with type, links, confidence, and
  provenance, for the human owner to approve, edit, or reject.
- Rebuild `index.json` from the `.md` files, since the index is a cache,
  not the source of truth.

You MUST NOT, on your own initiative:
- Change this README, any template, the frontmatter schema, the file-naming
  rule, the folder structure, the link syntax, the tag vocabulary, the
  index shape, or any other schema or primitive in this vault — even if
  you think you know a better way, even if the change looks small, like a
  cleanup, a normalization, a rename, or a refactor.
- Silently edit, rename, merge, delete, or supersede an existing note,
  template, or map, except for the exact note change the human owner
  explicitly instructed in this conversation.
- Resolve a conflict between this README and a note or template by editing
  this README or the template. The README wins: flag the conflict to the
  human owner and keep using the README version.

If you think the schema is wrong or could be better, propose the change to
the human owner, explain why, and keep using the existing schema until then.
A general instruction like "improve the system" is NOT approval for a schema
change. Only the human owner may change the schema, by explicitly editing
this README, or by explicitly instructing an agent to make a named change —
and that instruction must name the change, not just ask for improvement.

## Principles

1. **Plain text first.** Notes are Markdown, UTF-8, no BOM, LF line endings.
   If every Markdown parser disappeared, the notes would still be readable
   text. No proprietary database is the source of truth.
2. **Elegant enough for agents, simple enough for humans.** One template
   per note type, one link syntax, one way to mark a note superseded, one
   way to mark implications. No hidden state, no app-specific metadata.
3. **Agnostic.** No dependence on Obsidian, Notion, Roam, a specific
   embedding model, or a specific AI provider. Tools may read the vault;
   the vault does not require them to be understood.
4. **Scale-invariant.** Works at 100 notes with no index, and at 100,000
   notes with a generated index. The index is a cache, not the source.
   It can be rebuilt from the .md files at any time.
5. **Self-describing.** An agent opening this folder cold should be able
   to read this README and then correctly use the rest of the vault,
   with no prior prompt and no custom tooling.
6. **Selective retrieval, even if context becomes free.** Direct hits
   first, supporting notes second, implications third. Reading everything
   degrades reasoning, even when it is cheap.

## What this system covers

- **Semantic memory (ideas, facts, connections):** permanent notes.
- **Episodic memory (what happened, what was decided, why):** daily notes
  and decision notes. These are not stripped of context.
- **Procedural memory (how to do things):** checklists and SOPs, as
  permanent notes with a procedural body, or as project notes.

Zettelkasten covers semantic best. It does not, by itself, cover episodic
or procedural well. This system adds those layers with different templates,
not by forcing them into permanent notes.

## File naming and folders

- One file per note. One canonical file, referenced many times. No
  filesystem symlinks. A wikilink is the portable symlink.
- Filename: `<id>-<slug>.md`
  - `id`: `YYYYMMDDHHMM`, e.g. `202610290221`. Unique, sortable, stable.
    Renaming the slug later does not change the id.
  - `slug`: lower-case, ASCII only, hyphens, no spaces. No non-ASCII in
    filenames (macOS NFD vs Linux/Windows NFC causes byte-different
    duplicates for accented characters).
  - Example: `202601010000-example-note.md`
- Folders are for type and time, not for topic. Topic lives in links
  and maps, not in folders. Deep folder hierarchies break as topics change.
  - `notes/<year>/` — permanent notes, sharded by creation year.
    Wikilinks are folder-agnostic, so moving a note to a year shard
    does not break links. Shard further by id prefix only if a single
    year folder exceeds tens of thousands of files.
  - `maps/` — Maps of Content (MOCs). A map is a curated retrieval set.
  - `literature/` — article / book summaries, source separated from take.
  - `decisions/` — decision notes.
  - `daily/` — daily logs, messy is fine.
  - `inbox/` — raw dumps, unprocessed. Process weekly, do not let the
    inbox become the vault.
  - `templates/` — templates below. Do not edit a template without
    updating this README.
- Body text may be any UTF-8. Filenames and frontmatter values stay ASCII.

## Frontmatter schema

Minimal, same for every note. All values ASCII, quoted if they contain
a colon, `#`, or start with a special character.

```yaml
---
id: 202610290221
created: 2026-10-29
updated: 2026-10-29
type: permanent
status: active
---
```

- `id`: matches the filename prefix.
- `created` / `updated`: ISO dates, note creation / last substantive update.
  This is not the same as the date of the subject. If a note is about
  2026, but written in 2031, `created` is 2031 and the subject date goes
  in the body.
- `type`: permanent, literature, decision, daily, project, person, map.
- `status`: active, seedling, superseded.
- `aliases` (optional, Obsidian compatibility only): a list containing the
  H1 title, so Obsidian can resolve `[[Title]]` links to a file named
  `<id>-<slug>.md`. An AI should resolve links by H1 title, not by
  filename, and does not need aliases. Aliases are a convenience, not
  part of the core schema.

Do not put critical information only in frontmatter. Title, first-sentence
summary, confidence, and provenance also go in the body, so they survive
a migration that drops YAML.

## Body structure

Every permanent note follows this shape. Other types use their template,
but keep the same discipline: summary first, provenance explicit.

```markdown
# Title, matching the slug

First paragraph: one-sentence summary of the idea, in your own words.

## Body

The idea, evidence, and counterpoint if any.

Confidence: high / medium / low, and why.
Provenance: user-reported, measured, assistant summary from public
sources, external article, etc. Separate what you know from how you know it.

## Links

Related:
- [[Other note]]

Supports:
- [[Note this supports]]

Contradicts:
- [[Note this contradicts]]

Implications for:
- [[Map or project this has implications for, even if it is not about it]]

## Source

- URL, book, person, date, or "user, 2026-10-29" — separate from your take.
```

Link types matter for retrieval. "Related" is not "supports" is not
"implications for." An agent should return them in different tiers,
not as one undifferentiated pile.

## Tags: controlled, narrow, and optional at first

Start with links only, plus status and type tags if needed. Add topic
tags only after retrieval actually fails, and only from a controlled list.

Add topic tags only after retrieval actually fails, and maintain the controlled list in `maps/tag-index.md`.

If tags are added, keep them to three dimensions, max 1-2 per note
per dimension, and maintain a synonym map in `maps/tag-index.md`:

- Type: already in frontmatter, do not duplicate as a tag.
- Status: already in frontmatter, do not duplicate as a tag.
- Domain: only if not obvious from links.

If a note needs 4+ tags, it is probably two notes, not one.

## Corrections and supersession: do not silently edit

If a note is wrong, do not delete it or rewrite it in place without a trace.

1. Old note: `status` flips to `superseded`, with a one-line reason
   and a link to the new note.
2. New note: links back to the old note, with a one-line reason for
   the change.
3. The wrong version remains, marked wrong. An evaluator in 15 years
   needs history, not just a current snapshot.

This is the same rule for any corrected conflation: once
corrected, it stays visible as corrected, not erased.

## Maps of Content

A map is not a folder and not a tag. It is a note that curates a cluster.

Example structure for `maps/`:

```markdown
# Example Topic

Direct: notes actually about this topic.
Supporting: notes that explain a mechanism this topic depends on.
Implications: notes that are not about this topic, but affect it.
```

"All my notes on war games" means "the war games map and everything it
lists," not "every file containing the phrase war game." The map is
maintained when notes are created, not reconstructed at query time.

## Querying: how any agent should ask

An agent should translate a natural-language request into a structured
query *before* touching the vault, and show that translation if it is
ambiguous.

Examples:

Narrow:
```
topic: example-topic
date: 2026
type: permanent, decision
degree: direct
return: list
```

Broad:
```
topic: example-topic
date: any
degree: direct, supporting, implications
return: map-first
```

- `topic`: the map or cluster, not just a keyword.
- `date`: created date, unless the query explicitly says subject date.
- `degree`: direct = links to the map; supporting = 1 hop from a direct
  note; implications = explicit implications link.
- `return`: `map-first` returns the map summary instantly, with drill-down
  on request. `list` returns the filtered notes.

Steps for an agent:
1. Parse topic, date, type, degree.
2. Filter `index.json` (see below) by those fields. Do not scan all .md
   files at query time.
3. Read only the top N .md files, in parallel, starting with direct hits.
4. Stream direct hits first, then supporting / implications, labeled by
   tier and by link chain, so the user can see why a note was included.

## Index: generated, not hand-written

`index.json` at the vault root is a cache, not the source of truth.
It is rebuilt on every write. If it is corrupted or a future tool does
not like JSON, rebuild it from the .md files.

Shape (one object per note, plus a terms map for keyword search):

```json
{
  "notes": {
    "202601010000": {
      "id": "202601010000",
      "title": "Example note title",
      "file": "notes/2026/202601010000-example-note.md",
      "created": "2026-10-29",
      "updated": "2026-10-29",
      "type": "permanent",
      "status": "active",
      "links_out": ["202601010001"],
      "links_in": ["202601010002"],
      "implications_for": ["202601010001"]
    }
  },
  "terms": {
    "example": ["202601010000"],
    "topic": ["202601010000"]
  }
}
```

- Keyword search uses `terms`.
- Filtering by date / type / status uses `notes`.
- Graph degree uses `links_out` / `links_in` / `implications_for`.
- Vector embeddings, if used, are stored separately, with only a
  pointer in this file. Embeddings are regenerable from the .md files;
  they are not the source.

At 100,000 notes, shard the index by year, `index-2026.json`, etc.,
same shape, same query logic.

## Workflow: capture, process, revisit

1. **Capture:** dump a thought, article, or decision in chat or in
   `inbox/`. No formatting required at capture time.
2. **Process (agent-assisted, weekly):** split into atomic notes, one
   idea per note, in your own words, with links, implications, and
   provenance. Move out of `inbox/`. Do not let the inbox become the vault.
3. **Revisit:** weekly, surface orphans (notes with no links in or out),
   stale seedling notes, and superseded notes that are still linked as
   active. Prune or connect, do not just accumulate.
4. **Query:** use the structured query form above. Prefer map-first
   for broad queries, list for narrow queries.

## Note creation triggers (decided 2026-11-04)

Capture is free. Promotion to a permanent / decision / literature /
project note is gated, and the agent arbitrates against this list,
showing its work — it does not silently promote chat into the vault.

A candidate is promoted only if it passes all of:
- Atomic: one idea, not a whole conversation.
- Durable: will matter in 30+ days, or to a future project, without
  the chat context.
- Own-words + provenance + confidence, separate from source.
- Linkable: connects to an existing map/note, or justifies a new map.
- Not transient: not a price, schedule, task status, or one-off opinion.
- Not already covered: if it is, link to the existing note instead.

Decisions: only with reasoning + alternatives considered, where the
*why* will matter later. Literature: only if the source will be
reused, with take separated from source.

Process:
- Agent proposes candidates in `inbox/` with type, links, confidence,
  provenance, and why it meets the criteria above.
- User approves / edits / rejects in weekly review, or same-day if urgent.
- `note::` is a fast-track override: promote this now, skip the queue.
- `don't note this` is a veto: the agent must not promote it, and must
  not re-propose it.
- No promotion of personal, medical, or sensitive detail without an
  explicit `note::`. This matters because the vault may be pushed to GitHub
  (use a private repo for personal notes).

Test data: notes created to test the schema are marked seedling/test
until explicitly approved as real.

## Longevity checklist

- [ ] UTF-8, no BOM, LF endings.
- [ ] Filenames ASCII only, id + slug.
- [ ] Frontmatter minimal, ASCII only.
- [ ] One idea per permanent note, summary first.
- [ ] Links typed, not just a pile.
- [ ] Source separated from take, confidence and provenance in body.
- [ ] Superseded notes kept, marked, linked to replacement.
- [ ] README updated when a rule changes.
- [ ] No proprietary database as source of truth.
- [ ] Index regenerable from .md files.

## Open design questions (not yet decided)

- Controlled topic tag vocabulary: deferred until retrieval fails.
  See Maps / tag-index, to be created only then.
- Daily note format: date file vs single log, to be decided on first
  week of use.
- Media handling (agreed 2026-10-29, principle, not a full spec):
  media is evidence, not a note. The durable store is the text note
  interpreting the media, with a transcript / timestamped observations
  for audio and video. Media files themselves live in `assets/` or a
  separate media store, named by note id, referenced by SHA256 hash,
  format, and duration, in open formats (MP4/H.264, WAV/Opus, JPEG/PNG).
  The media store backend is replaceable (local drive, S3, Glacier, or
  whatever succeeds them): the vault references a logical identifier
  and hash, not a provider-specific path, so migrating storage does
  not rewrite notes.
  Do not put large media in Git with the text vault. If a codec dies,
  the transcript and observations survive. Specific media types to be
  captured are not yet known, so no media-specific template is added yet.
