# Review — workflow, review queue, longevity, before you push

Westmarch spec file. Read `README.md` first — it is the source of truth, it wins on conflict, and it tells you which spec file to read for your task. Read this file only for the task it covers; do not read every spec file for every question.

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
  to a person even with no name — see spec/safety.md, Sensitive information.

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

