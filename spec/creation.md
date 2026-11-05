# Creation — daily notes, links, titles, schema, tags, supersession, maps, promotion

Westmarch spec file. Read `README.md` first — it is the source of truth, it wins on conflict, and it tells you which spec file to read for your task. Read this file only for the task it covers; do not read every spec file for every question.

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
  ids, see spec/setup.md, Timezone and renaming.
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
  in the body. See spec/setup.md, Timezone and renaming.
- `type`: permanent, literature, decision, daily, project, person, map.
- `status`: active, seedling, superseded.
- `superseded_by`: id of the replacing note, required only when
  `status` is `superseded`, omitted otherwise. See Corrections and
  supersession.
- `supersedes`: id of the note this note replaces, present only on
  a note that supersedes another, omitted otherwise.
- `aliases`: do not use. Aliases were previously kept for Obsidian
  compatibility; that support has been dropped in favour of AI
  agnosticism (see README.md, Principles). Resolution is by id, as defined
  in Link resolution above, not by alias, title, or filename.

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
— and that judgment is preserved in the index (see spec/query.md, Index), not
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
`decisions/` (same mechanism as the Index protocol in spec/query.md). For example:

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
  `templates/proposal.md` — see spec/review.md, Review queue, for the form,
  status, veto, and expiry rules. A proposal is not a note, and a
  seedling note is not a proposal: do not conflate them, or the
  vault will accumulate hundreds of seedling candidates and only
  a handful of actual notes.
- User approves / edits / rejects at the review cadence, or same-day if urgent.
  The cadence adapts to the backlog — see spec/review.md, Adaptive cadence.
- `note::` is a fast-track override: promote this now, skip the queue.
- `don't note this` is a veto: the agent must not promote it, and must
  not re-propose it — see Review queue, veto list.
- No promotion of personal, medical, or sensitive detail without an
  explicit `note::`. This matters because the vault may be pushed to GitHub
  (use a private repo for personal notes).

Test data: notes created to test the schema are marked seedling/test
until explicitly approved as real.

