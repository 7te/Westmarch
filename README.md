# Personal Knowledge System — AI-Agnostic, Long-Lived

This is a place to keep what you know, in plain files you own, so you do
not have to remember everything yourself or depend on one app, one company,
or one AI. You write short notes, one idea each, link them to related ideas,
and record where each idea came from and how sure you were about it. Years
later, you — or any AI you hand this folder to — can read those notes, find
the related ones, and see what changed and why, without needing special
software to understand them.

In practice it is simple: capture anything quickly in `inbox/` or `daily/`,
then at your chosen review cadence turn only the ideas that will still matter in 30 days into
permanent notes using the templates in `templates/`, link them, and collect
topics into maps in `maps/`. Old notes are never silently deleted — when one
is wrong, it is marked superseded and linked to its replacement, so the
history stays visible. It is not a proprietary database, not a subscription
app, and not a pile of chat logs. It is your own long-term memory, in text
files that will still open in 20 years. The index is a derived database
(see Index below) — built from those text files, rebuildable at any time —
not a separate proprietary store.

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
- Rebuild the per-year index files (`index-<year>.json`) and the root
  manifest (`index.json`) from the `.md` files, since the index is a cache,
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

## Sensitive information: assume it could become public

This system does not ban sensitive notes. That decision belongs to the human
owner, who decides how much trust to place in the system. But make that
decision with this assumption in mind:

Treat anything you put in this vault — especially if you push it to GitHub,
sync it to a cloud drive, or hand the folder to an AI — as if it could end
up publicly available on the open internet, even if the repo is set to
private today. A private setting can be changed by mistake, a folder can be
shared, forked, or copied, a sync service or AI tool can retain or expose
it, and a note once pushed is hard to fully retract from git history,
forks, and caches. Private reduces exposure; it does not eliminate it.

Strongly discouraged by default, not forbidden:
- Personal identifying information: full names, addresses, dates of birth,
  government or account IDs, phone numbers, and similar identifiers.
- Secrets: passwords, API keys, tokens, private keys, and recovery codes.
  These never belong in a note vault.
- Financial, business, or client data: account numbers, salaries, pricing,
  contracts, internal business figures, and anything a client or employer
  gave you in confidence.
- Medical and other sensitive personal detail.
- Indirect identifiers: details that could be used as heuristics to
  identify an individual, even with no name attached. A note can still
  identify someone by combination — for example exact dates plus a
  neighborhood, workplace, role, or shift pattern, plus a distinctive
  biography, injury, workplace story, or other rare trait. Removing a name
  is not anonymizing if the remaining details triangulate to one person.
  Generalize instead where you can: year instead of exact date, city
  instead of neighborhood, role instead of employer, and leave out rare
  details that are not needed for the idea itself.

If you consciously choose to include such detail, do it deliberately, not
by default: anonymize or redact where you can, keep the sensitive detail in
a separate local-only or encrypted store that is never pushed, synced, or
handed to an AI, and do not push personal notes into a public repo — this
boilerplate repo is a template, not a place for your notes.

Agents: do not promote personal, medical, or sensitive detail without an
explicit `note::`, and when a human owner asks to add such detail, warn
them of this assume-public rule first and get explicit confirmation for
that specific note. That check covers indirect identifiers too, not just
direct PII — if a proposed note combines enough details to triangulate
to a person, flag it, generalize it, or leave the identifying details out.

## Principles

1. **Plain text first.** Notes are Markdown, UTF-8, no BOM, LF line endings.
   If every Markdown parser disappeared, the notes would still be readable
   text. No proprietary database is the source of truth. The index *is* a
   database — a derived one, built from the `.md` files, rebuildable at any
   time, and never a separate store. The `.md` files are the authoritative
   source; the index is the query source. On any conflict, the `.md` file
   wins, and the index is regenerated. See Index below for the rebuild
   contract.
2. **Elegant enough for agents, simple enough for humans.** One template
   per note type, one link syntax, one way to mark a note superseded, one
   way to mark implications. No hidden state, no app-specific metadata.
3. **Agnostic.** No dependence on Obsidian, Notion, Roam, a specific
   embedding model, or a specific AI provider. Tools may read the vault;
   the vault does not require them to be understood. Obsidian
   compatibility is not a design goal and has been dropped in favour
   of AI agnosticism: if Obsidian happens to resolve a link, that is
   incidental, not guaranteed, and it must never constrain the link
   format below.
4. **Scale-invariant.** Should work at 100 notes with no index, and at 100,000
   notes with a generated index. The index is a cache, not the source.
   It can be rebuilt from the .md files at any time. 100,000 is a design
   goal, not a tested claim — no vault using this system has been run
   at that size, and the claim is "should", not "does".
5. **Self-describing.** An agent opening this folder cold should be able
   to read this README and then correctly use the rest of the vault,
   with no prior prompt and no proprietary or required tooling.
   "No custom tooling" means: no proprietary tool, and no tool is
   required to read or use the vault. The vault is plain `.md` files
   plus this README, and that alone is sufficient. Any tooling is
   small, standard, replaceable, and optional — a helper may exist,
   but the vault must never depend on it. A human can maintain the
   vault by hand, slowly: the index is derived from note frontmatter
   (id, title, file, type, status, dates, tags, typed links), so a
   person with a text editor can rebuild or correct an index entry
   from the `.md` file it mirrors, and an agent with no code
   execution can do the same. That is slow and error-prone at
   scale, which is why a helper is useful — not why one is required.
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
  - `id`: `YYYYMMDDHHMM` in UTC, e.g. `202601010000`. Unique, sortable, stable.
    Renaming the slug later does not change the id. See Timezone and
    renaming below — UTC is the structure, local time is included
    in the body only where it is relevant.
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
  - `daily/` — daily logs, one file per day, hard-coded —
    see Daily notes below. Messy is fine.
  - `inbox/` — raw dumps, unprocessed. Process at the review cadence set at setup, do not let the
    inbox become the vault.
  - `templates/` — templates below. Do not edit a template without
    updating this README.
- Body text may be any UTF-8. Filenames and frontmatter values stay ASCII.

## Initial setup: sharding and review cadence — set once, follow prescriptively

Three choices are made at initial use, via the agent, in plain language
— not guessed per note, and not hard-coded as universal rules.
Record all three as decision notes in `decisions/`, like the index and
link-typing decisions, with date, options considered, choice,
fallback, and review trigger.

**1. Sharding architecture.** Some people generate notes fast enough
that sharding matters quickly, so do not start flat and shard later.
At initial use, the agent presents the example structure in File
naming and folders above — `notes/<year>/`, `maps/`, `decisions/`,
`literature/`, `daily/`, `inbox/`, `templates/` — as a concrete
example, not as a description to interpret. The user accepts that
example, or customises it (for example `notes/<year-month>/`, a
different shard key, or a different layout altogether). That choice
is then followed prescriptively: create the shard folder if it does
not exist, place the note there, never flat, never in a topic
folder, unless the recorded decision says otherwise. Do not
re-interpret sharding per note, and do not silently change the
structure later — a change is a new recorded decision, made by the
human owner.

**2. Review cadence.** Weekly is the author's personal preference,
not a system requirement. At initial use, the agent asks the user
to set a review cadence, presenting weekly only as an example:
daily, weekly, monthly, yearly, or a custom cadence the user picks.
Record that choice, and follow it. Where this README says "at the
review cadence", it means the cadence set at setup, not necessarily
weekly.

**3. Granularity preference.** Language is arbitrary, and so is
granularity of thought — unlike 1+1=2, there is no immutable size
for "one idea". So the system does not impose a universal atomic
size. At initial use, the agent asks the user, in plain language,
how fine-grained they want notes to be: fine (split aggressively,
one claim per note), medium (one topic per note, with related
claims together), coarse (broader topic notes, split only when a
topic clearly has two independent halves), or a preference in
their own words. Record that choice as a decision note, and follow
it prescriptively thereafter, using the worked examples below as
calibration for what that preference looks like in practice. The
user can change granularity at any time, and the agent checks,
at review, whether it still fits — see Review queue, manual
configuration, for the same pattern.

The agent checks up on preferences later, rather than assuming the
initial choice is permanent: at a review, or periodically, ask in
plain language whether the cadence — and, if volume has changed,
the sharding — still fits. The human can change either at any
time, and the change is recorded as a new decision note and
followed prescriptively thereafter.

If no setup choice exists yet — a cold agent opening the vault
with no decision note — default to the example structure
(`notes/<year>/`) and a weekly cadence, say plainly that those are
defaults, and ask the user to confirm or change them at the next
conversation. A default is a fallback, not a permanent guess.

**Boilerplate note, prompt before treating it as real.** If a
seedling note titled "Boilerplate note" exists in `notes/`, the
first agent opening this vault must prompt the human owner, in
plain conversation, before treating it as a personal idea: do you
want to keep it, move it, or delete it? Do not delete it silently,
and do not keep it silently as if it were a personal idea. Follow
the human owner's exact instruction for that note.

## Daily notes: one file per day, hard-coded

Daily format is decided, not open, and not a setup choice:

- One file per day, in `daily/`, named `<YYYY-MM-DD>.md` — for
  example `daily/2026-11-04.md`. No weekly log, no monthly log,
  no single append-only log. A user who wants a different daily
  format can adjust the system for their own use case, as with
  any other rule, but the boilerplate default is not ambiguous.
- Date is the user's local timezone date, not UTC. A note written
  at 00:30 local is that local date's file, even if UTC says
  otherwise. If the user's local timezone is not known, ask, and
  record it — do not silently default to UTC.
- Id is derived from the date, deterministically: `<YYYYMMDD>0000`
  — for example, `202611040000` for 2026-11-04. Do not use the
  creation time as the daily id, or the same date could get two
  different ids. One date, one file, one id.
- Daily id is the exception to the UTC rule, on purpose: a daily
  note represents a local day, not a UTC moment, so its id is
  derived from its local date, as above. Its UTC creation moment
  may therefore fall on a different UTC date near midnight —
  that is expected, not corruption, because the daily id is a
  day-identifier, not a timestamp. General notes always use UTC
  ids, see Timezone and renaming below.
- If a daily file for that date already exists, append to it
  under the existing headings (What happened, Decisions made,
  Ideas to process), in chronological order, with a time if known.
  Do not create a second file for the same date, and do not
  overwrite the existing file.
- Daily vs inbox: a capture tied to a specific day's events goes
  to that day's daily file. A raw idea, article, or thought not
  tied to a day goes to `inbox/`. Do not create both for the same
  capture. Daily notes are episodic source material for promotion
  at review, not permanent answers: they are indexed with
  `type: daily`, but they are never direct hits for a topic query
  — only for a history / date query, or as provenance for a
  promoted note.

## Timezone and renaming

**Timezone: UTC is the structure, local time is context.**

Ids and `created` / `updated` dates are always in UTC. UTC has no
DST, no travel shift, no date-line jump, and no agent-timezone
mismatch — an id sorted in UTC is creation order, always, even
for someone who flies internationally a lot. Local timezone, if
used for ids, can theoretically corrupt that: DST fall-back
repeats an hour, flying west moves local time backwards, crossing
the date line jumps the local date, and an agent using its own
system timezone as "local" is not even the user's local. UTC
avoids all of that, by design.

Local time is not lost — it is included where it is relevant,
in the body, in addition to the UTC structure, never instead of
it. Prompt the agent to include local time if it is relevant
for a note: an event, a meeting, travel, a daily log, anything
where "when, locally" matters for understanding the note. Include
it with its timezone, for example "Local: 2026-11-04 23:30 EST
(UTC-5)", alongside the UTC id / created date. If local time is
not relevant — a timeless idea, a principle, a summary — do not
add it just for completeness. Subject dates in the body should
likewise carry their local timezone if that timezone matters for
the subject, otherwise UTC is sufficient.

At setup, record the user's home timezone as a decision, for
daily filenames and for local-time inclusion. That recorded
timezone is for context, not for ids: ids stay UTC regardless
of where the user is physically, so travel does not corrupt
them. If a note's relevant local time is a travel local time,
not home time, use that travel local time, labelled, in the body.

**Renaming: id never changes, except for a duplicate / placeholder.**

- Slug rename: rename the file `<id>-<old-slug>.md` to
  `<id>-<new-slug>.md`, keep frontmatter id unchanged, update
  the index file path for that id. Id-form links are unaffected
  (they use id, not slug), map lists are unaffected (they use
  id-form links).
- Title rename: change the H1 only, keep id and filename
  unchanged. Display text in incoming id-form links becomes
  stale — cosmetic only, agent may propose updating display in
  review, must not silently rewrite all incoming links. See Link
  resolution below.
- Folder move: move the file to a different year folder, keep id
  unchanged, update the index file path for that id. Links are
  unaffected, because resolution is by id, not by path.
- Id change: never, in normal cases. Id is stable. The only
  exception is a duplicate or placeholder id, which is already
  a pre-push hold: the human decides which note keeps the id,
  the other gets a new UTC id, all links to the renamed note are
  updated to the new id, the index is updated, and the change is
  recorded as a decision. Do not change both ids, and do not
  change an id silently.

## Link resolution: id is canonical, title is display only

Canonical link form, for all new links:

```
[[<id>|<Display Title>]]
```

Example: `[[202601010001|Example note title]]`. The part before the
pipe is the target: the note's id, exactly as in its frontmatter and
filename prefix. The part after the pipe is display text only — the
note's title at link time, for human reading. An agent resolves the
link by id, via the index or by frontmatter id, regardless of the
note's current slug, folder, or H1 title.

That is deliberate, and it solves the rename problems that title
links cannot:

- Renaming a slug does not break a link, because the id does not change.
- Renaming an H1 title does not break a link, because the target is
  the id, not the title. Stale display text is cosmetic only: an
  agent may propose updating it in review, but it must not silently
  rewrite all incoming links, and a stale display must never be
  treated as a broken link.
- Moving a note to a different year folder does not break a link,
  because resolution is by id, not by path.

Legacy title-only links, `[[Title]]`, if encountered in older notes:
resolve them via a title-to-id map, and only if there is exactly one
match. If there are two or more matches, or no match, **fail loudly**:
do not guess, do not return the first match, do not return a wrong
note as if it were right. List the candidates (or the lack of any),
ask the human owner to pick, and record the corrected id link.
Failing loudly is the correct behaviour here, not a bug to work around.

Duplicate titles are not a rare edge case. At 10 notes they are
uncommon; as a vault grows to hundreds or thousands of notes, generic
and near-duplicate titles become inevitable — "Meeting notes",
"Project update", "Ideas", case and punctuation variants of the same
title, and so on — and they make indexing, search, and legacy title
resolution materially harder. That is why the canonical form is by id,
and why the title directive below matters at scale.

## Titles: specific, not vague

Agents should try to avoid vague titles in general, and propose
specific ones at creation time. A title should be specific enough to
distinguish this note from other notes on a similar topic, without
needing its body to explain what it is about.

Avoid, as a title alone: "Notes", "Ideas", "Meeting", "Project",
"Update", "Thoughts", and similar single-word or generic titles.
Prefer a title that carries topic, scope, or date where needed, for
example "Meeting notes - restaurant SaaS pricing, 2026-11-04" rather
than "Meeting notes".

This is a directive, not a ban and not a validator: a note is not
rejected for a vague title, and a human owner may keep a vague title
if they choose to. The agent's job is to propose a specific title,
flag a vague one in review with a more specific alternative, and
otherwise follow the owner's choice. As the vault grows, vague titles
are a scaling cost — more collisions, harder search, more ambiguous
legacy links — so the earlier specificity is introduced, the cheaper
it is.

## Frontmatter schema

Minimal, same for every note. All values ASCII, quoted if they contain
a colon, `#`, or start with a special character.

```yaml
---
id: 202601010000
created: 2026-01-01
updated: 2026-01-01
type: permanent
status: active
---
```

- `id`: matches the filename prefix.
- `created` / `updated`: ISO dates in UTC, note creation / last substantive update.
  This is not the same as the date of the subject. If a note is about
  2026, but written in 2031, `created` is 2031 and the subject date goes
  in the body. See Timezone and renaming below.
- `type`: permanent, literature, decision, daily, project, person, map.
- `status`: active, seedling, superseded.
- `superseded_by`: id of the replacing note, required only when
  `status` is `superseded`, omitted otherwise. See Corrections and
  supersession.
- `supersedes`: id of the note this note replaces, present only on
  a note that supersedes another, omitted otherwise.
- `aliases`: do not use. Aliases were previously kept for Obsidian
  compatibility; that support has been dropped in favour of AI
  agnosticism (see Principles above). Resolution is by id, as defined
  in Link resolution below, not by alias, title, or filename.

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
Provenance: exactly one value from the controlled list below.
Separate what you know from how you know it.

## Links

Related:
- [[202601010001|Other note title]]

Supports:
- [[202601010002|Note this supports]]

Contradicts:
- [[202601010003|Note this contradicts]]

Implications for:
- [[202601010004|Map or project this has implications for, even if it is not about it]]

## Source

- URL, book, person, date, or "user, 2026-01-01" — separate from your take.
```

**Confidence and provenance: exact values only, controlled list.**

Confidence must be exactly one of:

- `high` — directly stated by the user, directly measured, or
  primary source verified.
- `medium` — secondary source, consistent evidence, or inference
  from strong evidence.
- `low` — tentative, single weak source, assistant inference,
  needs verification.

Provenance must be exactly one of:

- `user-stated` — user said it directly, in conversation / message.
- `user-measured` — user measured it themselves and reported the
  number, for example HR push-ups 50, pull-ups 18.
- `measured` — agent / system measured it directly, for example
  file read, hiscores check, commit verified back from GitHub.
- `observed` — observed in use, not formally measured, for
  example an agent generated an index on its own.
- `external-source` — external public source, with citation in
  `## Source` (URL, book, date).
- `assistant-summary` — summary / synthesis of other notes /
  conversation, no new primary evidence.
- `inferred` — inference from evidence, not directly stated or
  measured.
- `unknown` — provenance unknown / not recorded, flag for review,
  do not use as a direct hit without verification.

Use the exact value, with the exact spelling and hyphen, no free
variants: not "user said", "from chat", "user-reported", "public
source", "measured" for a user measurement, or any other near
synonym. This is strict on purpose, like a pure function: same
input, same value, every agent, every time, so provenance can be
reliably filtered by exact string match. If a note's main claim
has mixed provenance, use the provenance of the main claim, note
the other provenance in the body / `## Source`, or split the note
if it is two independent claims with different provenance, see
Note-creation triggers.

If no value fits, do not invent a new one. Flag it in review and
ask the human owner: pick the closest value, split the note, or
add a new value. Only the human owner changes this vocabulary,
by editing this README or instructing a named change, following
the schema guard — an agent must not add a value on its own
initiative. A human owner who wants looser classification is
welcome to loosen it in their own copy: change the list in their
README, record the decision, and follow the changed list
prescriptively thereafter. That is their decision to make, not
the agent's, and not this boilerplate's default.

Link types matter for retrieval. "Related" is not "supports" is not
"implications for." An agent should return them in different tiers,
not as one undifferentiated pile, and it must label supports and
contradicts distinctly — a note that argues against an idea must never
be returned as if it supported it.

## Link typing: preserve all four types, without tedious manual work

All four link types are preserved: Related, Supports, Contradicts, and
Implications for. They are a human judgment — whether a note supports,
argues against, merely relates to, or has implications for another idea
— and that judgment is preserved in the index (see Index below), not
flattened into a single "related" pile.

Humans are not expected to do tedious classification work from scratch.
The work is split like this:

- **Agent proposes first.** When an agent creates or links a note, it
  proposes a type for each link, with a brief reason, using the
  definitions in Body structure above.
- **Human approves or corrects, at the review cadence set at setup.** The human is
  an editor, not a classifier: approve the proposed types, or correct
  the ones that are wrong. No link needs to be classified by hand
  unless the agent's proposal is wrong.
- **Ambiguous means Related.** If a link's type is not clear, default
  to Related. Do not force a false precision — a vague Related is
  better than a wrong Supports or Contradicts, and it can be
  re-typed later when the relationship becomes clear.
- **Untyped links are Related.** A link with no type given is treated
  as Related, not rejected. The system does not refuse a note for
  lacking a typed link.

**Setup choice, recorded like the index decision.** At setup, the
agent asks the human owner, in plain language, how strict link typing
should be in this vault, and records the answer as a decision note in
`decisions/` (same mechanism as the Index protocol above). For example:

- Light (recommended for most vaults, and for non-technical owners):
  default to Related, and only mark Supports, Contradicts, or
  Implications for when it is obvious. The agent does the obvious
  cases, the human corrects mistakes in review.
- Strict: the agent proposes a type for every link, and the human
  reviews every proposal at the review cadence set at setup.
- Minimal: links are Related unless the human or agent explicitly
  marks otherwise, for a specific note or argument.

The agent follows the recorded setup choice until the owner changes
it, and it does not re-ask on every note. If no setup choice exists
yet, default to Light: Related unless obvious, agent proposes,
human corrects in review. That default is what keeps the work from
becoming tedious — the human only touches the exceptions, not every
link.

## Tags: controlled, narrow, and optional at first

Start with links only, plus status and type tags if needed. Add topic
tags only after retrieval actually fails, and only from a controlled list.

Add topic tags only after retrieval actually fails, and maintain the controlled list in `maps/tag-index.md`.

If tags are added, there is no numeric limit — no max 3, no 4+
split trigger, no 1-2 per dimension, no total cap. Those numbers
contradicted each other, and a number an agent can game is not a
good rule for an arbitrary thing like tagging. Maintain a synonym
map in `maps/tag-index.md`, and keep tags controlled and narrow:

- Type: already in frontmatter, do not duplicate as a tag.
- Status: already in frontmatter, do not duplicate as a tag.
- Domain / topic: only if not obvious from links, and only from
  the controlled list in `maps/tag-index.md`.

If a note needs many tags, consider whether it is two notes, not
one — as a prompt to think, not as a trigger to split. The agent
shows its work in the proposal file: why these tags are justified,
what the closest worked example is, and whether it thinks a split
would help. The human, at review or anytime in conversation, can
split, merge, or retag a note with a prompt based on what they
want — "split this note into pricing and margin notes", "drop
the `shift4` tag, it's in the body already", "keep all 6 tags,
this really is one idea" — and the agent does that exact change,
per the schema guard. The human is the final editor on tags, not
a number.

This decision is sealed until a long-term issue with it is
discovered: if, over time, over-tagging or under-tagging becomes
a real problem in practice, revisit this rule, record a new
decision, and follow that instead. Do not change it silently on
a single bad example.

## Corrections and supersession: do not silently edit

If a note is wrong, do not delete it or rewrite it in place without a trace.

Vaulting is good: the old version stays in the vault, marked, linked,
and retrievable. A succession chain of changed opinions is important
context, for the agent's context window and for the end user — it
shows what was believed, when, and why it changed, not just the
current snapshot. That is why superseded notes are deprioritised,
not excluded and not deleted.

Machine form, so an index and an agent can follow the chain without
parsing body text:

1. Old note: `status` flips to `superseded`, and frontmatter gains
   `superseded_by: <id>` — the id of the note that replaces it,
   required when `status` is `superseded`. The first line of the
   body is a one-line reason for the change, in a fixed place, so
   a human reading the old note sees it immediately.
2. New note: frontmatter gains `supersedes: <id>` — the id of the
   note it replaces — and links back to the old note, with a
   one-line reason for the change.
3. The index mirrors both fields, so a query can follow a chain
   A → B → C to the current note without reading every `.md`
   file, and can tell which notes in a chain are themselves
   superseded.
4. A chain is followed to the end: if B is itself superseded by C,
   the current note is C, not B. Do not stop at an intermediate
   superseded note and present it as current.

Querying rule — deprioritise, do not exclude:
- A superseded note is never returned as a direct hit alone.
- When a query matches a superseded note, return the current
  note first (following `superseded_by` to the end of the chain),
  then the superseded note after active notes, labelled
  `superseded`, with its reason and its replacement link shown.
- The same applies to maps: prefer current notes as Direct;
  a superseded note listed in a map must be labelled superseded,
  with its replacement shown.
- On an explicit history query — "why did this change?", "what did
  I used to think?", a contested new note, or a thin new note that
  does not carry forward the old reasoning — surface the full
  chain, in order, labelled, so the agent and the user can see
  the succession, not just the endpoint.

This is the same rule for any corrected conflation: once
corrected, it stays visible as corrected, not erased, and its
place in the chain stays machine-readable.

## Maps of Content

A map is not a folder and not a tag. It is a note that curates a cluster.
Note the vocabulary difference, deliberately: a map's Direct /
Supporting / Implications headings are retrieval tiers (is it about
the topic, one hop away, or affecting it?), while a note's Related /
Supports / Contradicts / Implications for headings are link types
(does it relate to, support, argue against, or have implications for
another idea?). Do not confuse the two — a Supporting note in a map
is not automatically a Supports link, and vice versa.

Example structure for `maps/`:

```markdown
# Example Topic

Direct: notes actually about this topic.
Supporting: notes that explain a mechanism this topic depends on.
Implications: notes that are not about this topic, but affect it.
```

"All my notes on Example Topic" means "the Example Topic map and everything it
lists," not "every file containing the phrase example topic." The map is
maintained when notes are created, not reconstructed at query time.

## Index protocol: environment check, user choice, recorded decision, fallback

The index is important, and it is not optional at scale. What is
configurable is *how* it is maintained in this vault, because not every
agent has the same tools, and not every vault is the same size. Do not
pick a universal method and do not drop the index entirely. Dictate it
for this vault, once, using this protocol:

1. **Environment check, silently, before any note work.** The agent
   checks what it can actually do here: can it execute code? Can it
   read and write files directly, or is it only receiving files in
   chat? How many notes are in the vault right now? Is there an
   `index.json` already, and does it match the notes on disk, or is
   it empty or stale? No user input is needed for this step.
2. **User choice, only where the answer changes the design.** If the
   environment already answers it — a small vault where scanning is
   trivial, or an agent with no code execution — the agent picks the
   obvious method and says what it picked, without interrogating the
   user. If the choice genuinely matters, the agent asks in plain
   language, not tool jargon: for example, "Your vault is small, I can
   just read your notes directly. Do you want me to keep a separate
   list as well, or keep it simple?" or "I can rebuild that list
   automatically when I have code access. Do you want that, or do you
   want it kept by hand so any AI can do it, even one that cannot run
   code?" The user decides based on their constraints and preferences.
3. **Recorded decision.** The choice is written as a decision note in
   `decisions/`, using the decision template: date, environment
   (tools available, vault size, index state), options considered,
   choice made, fallback (see below), and review trigger. The next
   agent reads that decision first and follows it. It does not re-ask
   the user.
4. **Fallback, always.** Whatever method is chosen, if the index is
   missing, stale, or unusable in this agent's environment, the agent
   falls back to maps first, then to reading the note files themselves,
   and verifies any index hit against the actual `.md` file before
   citing it. An empty or stale index must never, on its own, produce
   an empty answer. The index is a hint and an accelerator, not the
   sole source of truth — the `.md` files are.
5. **Re-ask only on material change.** Revisit the decision only if
   the environment changes materially (a new agent with very different
   tools), the vault crosses a size threshold where the chosen method
   stops being practical, or the fallback keeps failing. A new agent
   session alone is not a reason to re-ask.

As a rule of thumb, not a hard threshold: under roughly 100 notes,
maps plus file reads are sufficient and a JSON index may be empty;
above that, a maintained JSON index should be kept, sharded by year
by default (see Index below). The recorded decision
for this vault may set a different threshold, and that decision wins
for this vault.

## Querying: how any agent should ask

An agent should translate a natural-language request into a structured
query *before* touching the vault, and show that translation if it is
ambiguous. It should first follow the index protocol above: use the
recorded index method for this vault, and fall back to maps and file
reads if that method is unusable here.

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
2. Use the recorded index method for this vault (see Index protocol
   above). If that method is unavailable here, or the index is empty
   or stale, fall back to maps, then to reading the `.md` files
   directly — do not return an empty answer from an empty index alone.
3. Read only the top N .md files, in parallel, starting with direct hits,
   and verify any index hit against the actual `.md` file before citing it.
4. Stream direct hits first, then supporting / implications, labeled by
   tier and by link chain, so the user can see why a note was included.

## Index: generated, not hand-written

The index is a derived database, not a proprietary one, and not
the authoritative source. By default it is
sharded by year: one file per year, `index-<year>.json`
(`index-2026.json`, etc.), each with the shape below, plus a root
`index.json` that is a manifest only — it lists the year shards,
their note counts, and freshness, not the full note entries.

**Rebuild contract.** The index is built from the `.md` files, and
only from them. It is rebuildable at any time, from scratch, with
no other input. The `.md` files are the authoritative source; the
index is the query source — the thing agents actually search, filter,
and follow links through at scale. On any conflict between an index
entry and its `.md` file, the `.md` file wins and the index entry is
regenerated. Before trusting an index entry for a citation, run the
freshness check below. Conflicts are never merged — discard and
regenerate. A stale index must never, on its own, produce an empty
answer — fall back to maps, then to file reads, per Index protocol.
Calling the index "a cache" does not change what it is in practice:
at scale, what the index contains is what an agent can find.
It is maintained according to the recorded index decision for this
vault (see Index protocol above). If it is corrupted or a future tool
does not like JSON, rebuild it from the .md files, using the method
recorded in that decision — or, if no decision exists yet, run the
index protocol first and record one.

An agent querying the vault reads the root manifest first, then only
the year shard(s) relevant to that query — never the whole index at
once. If a single year shard itself gets too large to read or query
practically, do not silently invent a new scheme. Explain the issue
to the human in plain language — how large the shard is, what is
getting slow or expensive, and what sharding options would help
(by month, by id prefix, by topic, or another) — work with them to
choose a more functional sharding solution, and record the choice
as a decision. The per-year default is the starting point, not the
final answer at every size.

### Index is committed, with a freshness check

The index files — `index-<year>.json` and the root manifest
`index.json` — **are** committed, and are deliberately **not** in
`.gitignore`. That is a decision, not an oversight, made for the
cold-agent case: a fresh clone, or an agent with no code execution,
benefits from a shared index built by a previous agent, instead of
starting from an empty index and scanning every file. Committing it
is what makes that sharing work.

Committing it does not make it the source of truth, and it does not
make it trustworthy on sight. An agent must run a freshness check
before trusting it:

- Compare the index against the notes on disk for the notes it
  intends to cite: id, file path, and `updated` date must match the
  actual `.md` frontmatter. If a cited note mismatches, verify from
  the `.md` file, and treat the index entry as stale.
- If the index's note count, or its list of ids, does not match
  the notes on disk overall, treat the index as stale, not as
  complete. A stale index must never, on its own, produce an empty
  answer — fall back to maps, then to file reads, per Index protocol.
- An agent that generates an index may include a `generated`
  timestamp / note count in it, and an agent reading it may use
  that as a first hint, but must not rely on a timestamp alone:
  verify cited notes against `.md` anyway.

Conflicts on the index files are never merged:
- In git, if an index file conflicts, discard both sides and
  regenerate it from the `.md` files, then commit the regenerated
  version. Merging two derived indexes by hand is meaningless —
  regeneration is the only correct resolution.
- In iCloud, if a conflicted copy appears, ignore the conflicted
  copy, regenerate from `.md` files, and delete the conflicted
  copy if possible. Do not cite from a conflicted index.
- Commit the index files in the same commit as the `.md` changes
  that caused it, not as separate index-only commits, except for
  a deliberate rebuild at review. That keeps each commit's index
  in sync with its notes, and keeps git history readable instead
  of buried under index churn.
- An agent with no code execution that cannot regenerate a stale
  index may still use a committed index after the freshness check
  for cited notes, but must flag the staleness to the human in
  plain language, not silently trust it.

Shape (one object per note, plus a terms map for keyword search):

```json
{
  "notes": {
    "202601010000": {
      "id": "202601010000",
      "title": "Example note title",
      "file": "notes/2026/202601010000-example-note.md",
      "created": "2026-01-01",
      "updated": "2026-01-01",
      "type": "permanent",
      "status": "active",
      "links": {
        "related": [],
        "supports": ["202601010001"],
        "contradicts": [],
        "implications_for": []
      }
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
- Index entries mirror `superseded_by` and `supersedes` when
  present, so a query can follow a supersession chain and
  deprioritise superseded notes without reading every `.md` file.
  See Corrections and supersession.
- Graph degree and retrieval tiers use `links.related`,
  `links.supports`, `links.contradicts`, and
  `links.implications_for`, kept distinct — they are not collapsed
  into a single list.
- Only outgoing typed links are hand-maintained in the index.
  Incoming links are derived from other notes' outgoing links at
  query or rebuild time, not edited by hand on every note — that
  would be the tedious work this system is trying to avoid.
  The older `links_out` / `links_in` fields, if present in an older
  index, are the union of the typed outgoing / incoming links
  respectively, and should be migrated to the typed form above.
- Vector embeddings, if used, are stored separately, with only a
  pointer in this file. Embeddings are regenerable from the .md files;
  they are not the source.

The index is sharded by year by default, `index-2026.json`, etc.,
same shape, same query logic. If a single year shard gets too large
to read or query practically, see the sharding instruction in the
Index section above: explain the issue to the human and agree a
more functional sharding solution, rather than silently inventing one.

## Workflow: capture, process, revisit

1. **Capture:** dump a thought, article, or decision in chat or in
   `inbox/`. No formatting required at capture time.
2. **Process (agent-assisted, at the review cadence):** split into atomic notes, one
   idea per note, in your own words, with links, implications, and
   provenance. Move out of `inbox/`. Do not let the inbox become the vault.
3. **Revisit:** at the review cadence, surface orphans (notes with no links in or out),
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

Worked examples — calibration, not tests. Language and granularity
are arbitrary, so these examples do not become a pass/fail
checklist an agent can game ("can I summarise this in one sentence
without 'and'?" — an agent will just write a sentence without
'and'). They are concrete cases to reason by analogy to, in light
of the user's granularity preference set at setup. In the proposal
file, the agent shows its work by naming the closest example below
and explaining, briefly, why this candidate is like it, or why it
differs. The human, at review, is the final editor on granularity.

- **Pass, single idea, durable.** "Sourdough starter needs feeding
  once a day at room temperature, twice if the kitchen is warm."
  One claim, still useful in 30+ days without chat context, links
  to a bread / fermentation map. Promote as a permanent note.
- **Fail, transient.** "The bakery on Main Street is open until
  5pm today." A schedule for today, not a durable idea. Do not
  promote. If the visit taught a durable lesson — for example,
  "warm kitchens accelerate fermentation" — promote the lesson,
  not the schedule.
- **Fail, too broad.** "Cooking." That is not one idea, it is a
  whole map. Do not promote as a permanent note. Either make it
  a map and put specific notes under it, or split it into
  specific notes: "Knife sharpening angle for a chef's knife",
  "Resting dough improves extensibility", and so on.
- **Split, two independent claims.** A conversation that yields
  both "Resting dough improves extensibility" and "Cold water
  slows yeast activity" contains two ideas that can be used,
  linked, and updated independently. Split into two notes, link
  them as Related, do not keep them as one note with two halves.
  At a coarse granularity preference, the same conversation might
  legitimately stay as one broader note — that is why granularity
  is a user choice at setup, not a universal rule.
- **Duplicate, link instead.** A candidate "Feeding a sourdough
  starter daily" when a note "Sourdough starter feeding schedule"
  already exists is already covered, even if the wording differs.
  Do not create a duplicate. Link to the existing note, and, if
  the candidate adds a genuinely new claim, add that claim to
  the existing note or as a linked note, with a reason.
- **Borderline, durable lesson from a transient event.** "I ran
  a 5K in heat and overheated, despite drinking water." The race
  date itself is transient. The lesson — "heat buildup, not leg
  fatigue, limits my running in humid conditions, and indoor
  treadmill paces understate that" — is durable, linkable, and
  reusable. Promote the lesson, not the event.


Process:
- Agent proposes candidates as proposal files in `inbox/`, using
  `templates/proposal.md` — see Review queue below, for the form,
  status, veto, and expiry rules. A proposal is not a note, and a
  seedling note is not a proposal: do not conflate them, or the
  vault will accumulate hundreds of seedling candidates and only
  a handful of actual notes.
- User approves / edits / rejects at the review cadence, or same-day if urgent.
  The cadence adapts to the backlog — see Adaptive cadence below.
- `note::` is a fast-track override: promote this now, skip the queue.
- `don't note this` is a veto: the agent must not promote it, and must
  not re-propose it — see Review queue, veto list.
- No promotion of personal, medical, or sensitive detail without an
  explicit `note::`. This matters because the vault may be pushed to GitHub
  (use a private repo for personal notes).

Test data: notes created to test the schema are marked seedling/test
until explicitly approved as real.

## Review queue: proposals, vetoes, expiry

**Proposal form.** A proposal is a file in `inbox/`, using
`templates/proposal.md`: frontmatter `type: proposal`,
`status: proposed`, candidate type, confidence, created date, plus
a one-sentence summary, why it meets the promotion criteria above,
links, source / provenance, and a Decision section. The pending
list is exactly the proposals with `status: proposed` — nothing
else counts as pending, and proposal files are not indexed as notes
and are never returned as direct hits.

**Status transitions, in plain language:**
- `proposed` → `approved`: agent creates the note, marks the
  proposal `approved`, records the created note's id in its
  Decision section, and moves it out of the pending list. The
  proposal file is kept, not deleted, so proposal → note is traceable.
- `proposed` → `rejected`: mark `rejected` with a one-line reason.
  Rejected is not vetoed: the same candidate may be re-proposed
  only with new evidence or new context, never automatically.
- `proposed` → `vetoed`: mark `vetoed` with a reason, and add it
  to the persistent veto list in `decisions/vetoes.md` (created by
  the agent on first veto if it does not exist: date, vetoed
  topic / proposal id, reason). Before proposing anything new, an
  agent must check that veto list first. A vetoed topic is never
  re-proposed, ever, unless the human explicitly revokes the veto
  — recorded as a new decision, with the veto list entry removed.
- `proposed` → `expired`: see Expiry below. Expired is not vetoed
  and not a permanent rejection: the human may revive it, and an
  agent may re-propose it only with new evidence or new context.

**Expiry rule, set at initial use, user-determined.** At initial
use, alongside sharding and review cadence, the agent asks the
user, with a strong, clear prompt in plain language, to set an
expiry rule — for example "expire after 1 month", "expire after
2 review cycles", "never expire", or a custom rule. Record the
rule, and the user's explicit do-not-ask choice (see below), as a
decision note in `decisions/`, like the other setup decisions.

Strong prompts and responses are fine, and expected, here. The
agent should not silently infer how often to ask from how the user
sounds — sentiment alone is unreliable, and a silent inference can
lock in a wrong rule or nag a user who just talks tentatively.
Instead, the agent asks explicitly, in conversation:

> "Do you want me to stop asking about expiry, or check again
> next review?"

and records the explicit answer:
- If the user says confidently, for example "just let them expire
  after 1 month and don't ask me again", the agent trusts that,
  applies the rule, and does not ask about the rule again unless
  there is a material change — many wanted proposals expiring,
  a cadence change, or the user saying the rule is not working.
  Do-not-ask about the rule does not mean silence about proposals:
  the agent still lists, at review, what expired and what is
  about to expire, so expiry is transparent, not hidden.
- If the user is hesitant, tentative, defers, or changes their
  mind, the agent asks more consistently at reviews, with concrete
  options — "Keep 1 month, try 3 months, or never expire?" — and
  says plainly why it is asking again, for example "you seemed
  unsure last time, so I'm checking again." It does not silently
  adjust its asking frequency based on a sentiment guess.
- If in doubt, default to asking, not to locking in.

**Manual configuration, if the method has a bug.** If a user
finds a bug with this method — proposals expiring too fast, a
rule that does not fit their cadence, a veto that should not
have stuck — the agent should first parse, in plain language,
why the rule exists as it is: to prevent the inbox piling up
with stale proposals, to keep the pending list reviewable, and
to make vetoes survive across agents. Then it should help the
user manually configure the parameters, to some extent: change
the expiry duration, change the do-not-ask flag, change how
often it re-asks, or revoke a veto. Record the new rule as a
new decision note, and follow it prescriptively thereafter.
Manual configuration covers parameters and preferences, not
breaking the core queue: proposals still have a status, vetoes
still are never re-proposed without an explicit revocation, and
a changed rule does not retroactively un-expire proposals
without the human saying so.

At each review, before expiry, the agent lists proposals about
to expire, so the human can approve, keep, or veto them before
they lapse — unless the user has confidently set do-not-ask on
the rule, in which case the agent still lists what expired, but
does not re-ask about the rule itself.

**Adaptive cadence: human-only promotion, but the queue must not explode.**

Promotion stays human-only — no agent ever promotes a proposal
to a note on its own. What adapts, when the human is away for
days or weeks, is the cadence and the proposing, so a backlog of
100 proposals does not make review impossible:

- **Backlog thresholds, set at initial use, recorded as a decision.**
  Defaults, unless the human sets different numbers: under 10
  pending proposals, normal cadence, agent proposes freely.
  At 10–25 pending, the agent stops proposing new low-priority
  candidates, consolidates duplicates into one proposal each,
  and surfaces the top 5 at the next session, asking the human
  to review sooner than the scheduled cadence.
  Above 25 pending, the agent stops proposing entirely until
  the backlog drops, consolidates duplicates, and presents a
  prioritised top 5–10, asking for a review now.
- **Cadence shortens when the backlog grows, lengthens when it stays small.**
  If pending proposals are growing, or the oldest pending proposal
  is older than two review cycles, the agent prompts for a review
  sooner than the scheduled cadence, in plain language. If the
  backlog stays empty or small for several cycles, the agent may
  propose lengthening the cadence, and the human decides — the
  agent does not silently change it.
- **No duplicate proposals.** Before proposing anything new, an
  agent checks the pending proposals and the existing notes.
  A candidate already proposed is merged into that proposal,
  with the new source noted, not proposed again as a second file.
  A candidate already covered by a note is linked to that note,
  not proposed as a duplicate.
- **Review stays doable.** At review, the agent lists the top
  proposals in full — highest durability, confidence, and
  linkability first — and the rest briefly, one line each, so
  the human can approve, reject, or veto the important ones
  without reading 100 full proposals. Expiry still applies
  per the expiry rule; adaptive cadence does not override it,
  and does not auto-promote anything.

## Longevity checklist

- [ ] UTF-8, no BOM, LF endings.
- [ ] Filenames ASCII only, id + slug.
- [ ] Frontmatter minimal, ASCII only.
- [ ] One idea per permanent note, summary first.
- [ ] Links typed, not just a pile.
- [ ] Source separated from take, confidence and provenance in body.
- [ ] Superseded notes kept, marked, linked to replacement.
- [ ] README updated when a rule changes.
- [ ] No proprietary database as source of truth; index is a derived
  database, rebuildable from .md files, .md wins on conflict.
- [ ] Index regenerable from .md files.

## Before you push: check, flag, hold

This system cannot control an AI, only the directives in this README.
So this rule is written as a conversation, not as a script, a gate,
or an error code — because the end goal is a fairly natural human
conversation, and a formal gate is exactly the kind of thing an
agent will either bypass silently or present as an incomprehensible
failure.

Before a git push, a publication, or handing the vault (or a note)
to another system — the step where retraction becomes hard — an
agent must check the notes being pushed, and only those notes, not
the whole vault every time:

Format, which breaks retrieval if wrong:
- Id is real and unique, not a placeholder like `YYYYMMDDHHMM` or
  `000000000000`, and not duplicated by another note.
- Frontmatter is complete, and the filename is ASCII `id-slug`.
- Links are in id form, or a legacy title link that resolves to
  exactly one note.
- File is UTF-8, no BOM, LF endings, so far as the agent can tell
  in its environment.

Safety, which is hard to retract once pushed:
- No secrets: passwords, API keys, tokens, private keys, recovery codes.
- No direct personal information, financial / business / client
  confidential data, or medical detail, unless the human has
  explicitly chosen to include that specific note.
- No indirect identifiers: combinations of details that triangulate
  to a person even with no name — see Sensitive information above.

A vague title alone does not hold a push: it is flagged in review
with a more specific alternative, per Titles above, but a note is
not rejected for a vague title.

If anything above flags, **do not push that note**. Tell the human
in plain conversation what flagged, in which note, and why it
matters — no error code, no ticket, no form to fill in. For example:
"I was about to push this note, but I noticed it contains X, which
could identify Y if the repo becomes public. I haven't pushed it —
do you want me to fix that, or push it anyway?"

Wait for an explicit override for that specific note. "Yes, push it
anyway" counts, for that note only. A general approval, an earlier
approval, or silence does not count. An override does not set a
precedent and does not change this rule: the next push is checked
again.

Saving a note locally is not blocked by this rule. The hold applies
to the push / share step. For an iCloud-only vault, where sync
itself cannot be held by a directive alone, flag before handing the
folder onward, and say plainly that sync may already have copied it.

A code-capable agent may run this checklist as a script it writes
itself. A no-code agent does the same checklist by reading. The
checklist and the hold are the same either way; a script is an
optional implementation, never a requirement, and it must not
change the checklist.

If the same flag keeps repeating — one agent consistently
generating bad notes and flagging them — say so plainly in
conversation, for example "this is the third note this week with
the same problem." That pattern is a signal to the human to change
the agent, the prompt, or the workflow. No directive in this README
can fix an agent's quality, and pretending otherwise would be a
gate that fails silently. The directive's job is to make the
problem visible, in conversation, before it becomes public.

## Open design questions (not yet decided)

- Controlled topic tag vocabulary: deferred until retrieval fails.
  See Maps / tag-index, to be created only then.
- Daily note format: decided 2026-11-04, one file per day, hard-coded
  — see Daily notes above. No longer an open question.
- Media handling (principle, not a full spec):
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
