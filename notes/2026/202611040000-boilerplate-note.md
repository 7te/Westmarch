---
id: 202611040000
created: 2026-11-04
updated: 2026-11-04
type: permanent
status: seedling
---

# Boilerplate note

This vault started as a clean boilerplate copy of an AI-agnostic
personal knowledge system, made to be handed to someone else and
used across AIs.

Kept, in general terms:
- README.md - the full system rules
- templates/ - note, decision, literature, map, project, person,
  daily, and proposal templates
- maps/tag-index.md - the tag-index structure, with the original
  owner's personal tag list removed
- index.json - a generated-index starter, shape only
- Folder structure: notes/, decisions/, maps/, literature/,
  daily/, inbox/, templates/

Removed, deliberately:
- All personal notes, maps, and decision notes from the original
  owner's vault
- All original note titles, tags, and index entries

How to use it across AIs:
1. Give any AI this folder, starting with README.md. The README is
   self-describing - an agent opening it cold should be able to use
   the vault with no prior prompt.
2. Capture freely to inbox/ or daily/, promote to notes/ only when
   a candidate is atomic, durable, in your own words with provenance
   and confidence, linkable, not transient, and not already covered.
3. Rebuild index.json from the .md files whenever it gets out of sync -
   it is a cache, not the source of truth.

For the next agent / new owner: prompt the human owner about this
note, before treating it as a real personal idea. Ask, in plain
conversation: do you want to keep this boilerplate provenance note,
move it somewhere else, or delete it? Do not delete it silently,
and do not keep it silently as if it were a personal idea. Follow
the human owner's exact instruction for this note, and only then
treat the vault as theirs.
