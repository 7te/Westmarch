# Query — index protocol, querying, and the index

Westmarch spec file. Read `README.md` first — it is the source of truth, it wins on conflict, and it tells you which spec file to read for your task. Read this file only for the task it covers; do not read every spec file for every question.

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

