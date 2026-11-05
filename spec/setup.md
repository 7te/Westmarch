# Setup — file naming, initial setup, timezone and renaming

Westmarch spec file. Read `README.md` first — it is the source of truth, it wins on conflict, and it tells you which spec file to read for your task. Read this file only for the task it covers; do not read every spec file for every question.

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
    see spec/creation.md, Daily notes. Messy is fine.
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

