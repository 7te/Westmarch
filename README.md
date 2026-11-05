# Westmarch — Personal Knowledge System, AI-Agnostic, Long-Lived

Westmarch is a place to keep what you know, in plain files you own, so you
do not have to remember everything yourself or depend on one app, one
company, or one AI. You write short notes, one idea each, link them to
related ideas, and record where each idea came from and how sure you were
about it. Years later, you — or any AI you hand this folder to — can read
those notes, find the related ones, and see what changed and why, without
needing special software to understand them.

## What inspired this, and what Zettelkasten is

This is inspired by the Zettelkasten — German for "slip box" — a way
of taking notes invented in practice by the sociologist Niklas
Luhmann, who kept tens of thousands of small paper slips, each with
one idea, each linked to other slips. The idea was not to file
information away, but to have a conversation partner: you write an
idea down in your own words, link it to what it relates to, and
later, when you come back to it, the links show you connections you
did not see when you wrote it.

In this system, a Zettelkasten note is:

- **One idea, not a whole topic.** A note is a single claim you can
  use, link to, and update on its own. If it needs "and" to hold
  together, it is probably two notes.
- **In your own words, with a source.** Not a copy-paste, not a
  quote alone. Where it came from, and how sure you were, are part
  of the note, so you can trust it later.
- **Linked, not filed.** Notes connect to each other with typed
  links — related, supports, contradicts, implications — and
  topics are collected into maps. You find a note by following
  links, not by remembering which folder you put it in.
- **Kept, not deleted.** When a note turns out to be wrong, it is
  marked superseded and linked to its replacement. The history
  stays visible, so you can see what you used to think, and why
  it changed.

The other inspiration is simpler, and more practical: apps die,
companies change their terms, subscriptions lapse, and proprietary
databases lock your own thinking inside a format only one tool can
read. Plain text files, in a folder you own, do not. That is why
this system is plain Markdown, no app required, and why any AI —
not one specific AI, not one specific app — should be able to read
it, add to it, and hand it back to you intact.

## Should you use it?

**Use it if:**

- You want a long-term memory, not just a place to dump things.
  You expect to come back to an idea in a month, a year, or ten
  years, and still understand it.
- You think by writing. Putting an idea in your own words, and
  linking it to what you already know, is how you work out what
  you actually think.
- You want to own your notes. Plain files, no subscription, no
  lock-in, readable in 20 years with any text editor.
- You want any AI to be able to help with it — to find notes,
  propose new ones, and link them — without that AI owning the
  system.

**Do not use it if:**

- You just want quick capture, and never want to look back. A
  notes app, a to-do list, or a chat log will do that faster, with
  less work.
- You want a task manager, a calendar, or a team database. This
  is not that. It is slow, deliberate, and for ideas, not for
  tracking what is due on Tuesday.
- You do not want to review what you captured. The system only
  works if, at your review cadence, you turn the ideas that will
  still matter in 30 days into permanent notes, and let the rest
  go. If you never review, the inbox just becomes a pile.
- You need heavy media, real-time collaboration, or a shared
  company wiki. Text notes can reference media, but media is not
  the store here, and this is a personal system, not a team one.
- You hate writing and linking. That is most of the work, and
  there is no way around it — an AI can propose and draft, but
  you are the editor, and you decide what stays.

If none of that fits, that is fine. A system you will not use is
worse than no system at all.

In practice it is simple: capture anything quickly in `inbox/` or
`daily/`, then at your chosen review cadence turn only the ideas that will
still matter in 30 days into permanent notes using the templates in
`templates/`, link them, and collect topics into maps in `maps/`. Old
notes are never silently deleted — when one is wrong, it is marked
superseded and linked to its replacement, so the history stays visible.
It is not a proprietary database, not a subscription app, and not a pile
of chat logs. It is your own long-term memory, in text files that will
still open in 20 years. The index is a derived database — built from
those text files, rebuildable at any time — not a separate proprietary
store. See `spec/query.md` for the rebuild contract.

This README is the source of truth for the rules. If a spec file, a note,
a template, or an index disagrees with this README, this README wins
until it is explicitly updated. The spec files in `spec/` hold the full
rules, in full; this README holds the summary, the map, and the routing.
Do not try to learn the system from this README alone if you are doing
a task that has a spec file — read that spec file.

## Sensitive information: assume it could become public

Treat anything you put in this vault — especially if you push it to
GitHub, sync it to a cloud drive, or hand the folder to an AI — as if
it could end up publicly available, even if the repo is set to private
today. A private setting can be changed by mistake, a folder can be
shared or copied, a sync service or AI tool can retain or expose it,
and a note once pushed is hard to fully retract from git history.

Strongly discouraged by default, not forbidden: personal identifying
information, secrets (passwords, API keys, tokens — these never belong
in a note vault), financial / business / client data, and medical and
other sensitive personal detail. Indirect identifiers count too — a
note can identify someone by combination, even with no name attached.
Generalize where you can: year instead of exact date, city instead of
neighbourhood, role instead of employer.

If you consciously choose to include such detail, do it deliberately,
not by default, and keep it in a separate local-only or encrypted store
that is never pushed, synced, or handed to an AI. Agents: do not promote
personal, medical, or sensitive detail without an explicit `note::`.

The full rule, with the agent warning and the indirect-identifier
check, is in `spec/safety.md`. Read it before adding sensitive detail.

## Principles

1. **Plain text first.** Notes are Markdown, UTF-8, no BOM, LF line endings.
   If every Markdown parser disappeared, the notes would still be readable
   text. No proprietary database is the source of truth. The index *is* a
   database — a derived one, built from the `.md` files, rebuildable at any
   time, and never a separate store. The `.md` files are the authoritative
   source; the index is the query source. On any conflict, the `.md` file
   wins, and the index is regenerated. See `spec/query.md` for the rebuild
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
   notes with a generated index. The index is a derived database,
   rebuildable from the .md files at any time. 100,000 is a design
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

## How to use it, in one paragraph

Capture anything quickly in `inbox/` or `daily/`. At your review cadence,
turn the ideas that will still matter in 30 days into permanent notes,
link them, and collect topics into maps. Approve, edit, or reject
proposals at review — an agent proposes, you decide. Old notes are never
silently deleted; they are marked superseded and linked to their
replacement.

## File map

- `README.md` — this file. Human summary, principles, file map, and
  agent routing. Source of truth; wins on conflict.
- `spec/safety.md` — Agent guard: what an agent may and may not change.
  Sensitive information: the full assume-public rule.
- `spec/setup.md` — File naming and folders. Initial setup: sharding,
  review cadence, granularity, expiry. Timezone and renaming.
- `spec/creation.md` — Daily notes, link resolution, titles, frontmatter
  schema, body structure, link typing, tags, supersession, maps, and
  the promotion criteria with worked examples.
- `spec/query.md` — Index protocol, querying, and the index: the derived
  database, the rebuild contract, per-year sharding, and the freshness
  check.
- `spec/review.md` — Workflow, review queue (proposals, vetoes, expiry,
  adaptive cadence), longevity checklist, and before-you-push.
- `spec/sync.md` — Sync and version control. Held, not decided — see
  below.
- `spec/open-questions.md` — Open design questions, not yet decided.
- `templates/` — Note templates. Use them exactly as written.
- `notes/`, `daily/`, `maps/`, `decisions/`, `inbox/` — Notes, daily
  notes, maps of content, decision notes, and raw captures / proposals.
- `index.json`, `index-<year>.json` — The index manifest and per-year
  index files. Derived, rebuildable, never the authoritative source.

## For AI agents: where to look next

Read this README first, then read **only the spec file for the task
you are doing**. Do not read every spec file for every question — that
is how rules get missed, and how context gets burned.

| Your situation / task | Read next |
|---|---|
| First install, or first time opening this vault | Read everything, in order: this README, then `spec/setup.md`, `spec/creation.md`, `spec/query.md`, `spec/review.md`, `spec/safety.md`. Then run Initial setup in `spec/setup.md` and record the setup decisions. A first install is the one time reading everything is correct. |
| Answering a question, finding notes | `spec/query.md` — index protocol, querying, and the index. Maps first, then the index, then the `.md` files. |
| Creating a note, or proposing one | `spec/creation.md` — templates, schema, links, tags, promotion criteria. |
| Reviewing proposals, or working the inbox | `spec/review.md` — review queue, vetoes, expiry, adaptive cadence. |
| Setting up, or changing sharding / cadence / granularity / expiry | `spec/setup.md` — initial setup, set once, follow prescriptively. |
| Safety, sensitive detail, or changing the schema | `spec/safety.md` — agent guard and the sensitive-information rule. Propose a schema change to the human; do not make it yourself. |
| Sync, git, iCloud, or a conflict | `spec/sync.md`, then `spec/query.md` for index conflicts. Sync itself is held — do not implement a provider-specific solution. |

### Agent with no filesystem access, no terminal, no code execution

You do not need to read about how to maintain the index beyond the
summary in this README. Read this README, then the spec file for your
task — usually `spec/creation.md` for a proposal, or `spec/query.md`
for a question — and work from the files you are given, maps first,
then the `.md` files themselves. Do not try to rebuild an index, do
not try to run a freshness check that needs code, and flag any index
staleness to the human instead of fixing it silently. A human, or a
full agent, can rebuild the index from the `.md` files.

### Full agent, with filesystem, terminal, and code execution

You can read everything relevant to your task, and you may rebuild the
index and run the freshness check — see `spec/query.md`. That does not
mean read every spec file for every question. Read the spec file for
your task, and only the other spec files that task actually touches.

### Head librarian — human decides, agent does not

If one agent has filesystem access and a full environment, and other
agents do not — for example, one agent with full access and others
working only in chat — the human may choose to use that full-access
agent as a **head librarian**. That agent alone maintains the index,
merges, resolves conflicts, and updates shared files. Other agents
propose only: they create proposal files in `inbox/`, they do not
update the index, they do not edit notes they did not create, and
they do not resolve conflicts.

This is a human decision, not an agent decision. An agent must not
appoint itself head librarian, and must not assume the role because
it has the tools for it. If the human has not designated a head
librarian, agents follow the normal rules, and two agents should not
conflict over updates — they theoretically should not, because
proposals are files, vetoes are checked first, and index conflicts
are discard-and-regenerate — but if a conflict does appear, flag it
to the human and stop, do not resolve it by editing shared state.

Record a head-librarian designation as a decision note in
`decisions/`, like the other setup decisions, naming which agent
holds the role and what it alone may do.

## Source-of-truth hierarchy

1. This README.
2. The spec files in `spec/`.
3. The templates in `templates/`.
4. Notes, maps, decisions, and the index.

If a spec file disagrees with this README, this README wins. If a
note, template, or index disagrees with a spec file, the spec file
wins. Flag the conflict to the human owner and keep using the
higher file's version. Do not resolve a conflict by editing the
higher file.
