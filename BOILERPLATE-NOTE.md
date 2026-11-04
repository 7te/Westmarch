# Boilerplate copy - what this is, and what was removed

This is a clean boilerplate copy of an AI-agnostic personal knowledge
system, made to be handed to someone else and used across AIs.

Kept, verbatim or generically cleaned:
- README.md - the full system rules, with personal examples replaced
  by generic examples
- templates/ - daily, decision, literature, map, permanent, unchanged
- maps/tag-index.md - the tag-index structure, with the original
  owner's personal tag list removed
- index.json - an empty generated-index starter, shape only
- Folder structure: notes/, decisions/, maps/, literature/, daily/,
  inbox/, templates/

Removed, deliberately:
- All personal notes in notes/2020/ and notes/2026/
- All personal maps, except the generic tag-index structure
- The original owner's decision note
- All original note titles, tags, and index entries
- All commit history - this copy starts with a fresh git init

How to use it across AIs:
1. Give any AI this folder, starting with README.md. The README is
   self-describing - an agent opening it cold should be able to use
   the vault with no prior prompt.
2. Capture freely to inbox/ or daily/, promote to notes/ only when
   a candidate is atomic, durable, in your own words with provenance
   and confidence, linkable, not transient, and not already covered.
3. Rebuild index.json from the .md files whenever it gets out of sync -
   it is a cache, not the source of truth.

How to push it to GitHub:
- This copy already has a fresh git init with a single initial commit
  and no remote. Create a new private repo, then:
  git remote add origin <your-new-repo-url>
  git push -u origin main
- Do not push it to the original owner's repo, and do not push
  personal notes into a public repo.
