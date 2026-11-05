# Safety — agent guard and sensitive information

Westmarch spec file. Read `README.md` first — it is the source of truth, it wins on conflict, and it tells you which spec file to read for your task. Read this file only for the task it covers; do not read every spec file for every question.

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

