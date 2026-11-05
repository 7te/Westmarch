# Open design questions (not yet decided)

Westmarch spec file. Read `README.md` first — it is the source of truth, it wins on conflict, and it tells you which spec file to read for your task. Read this file only for the task it covers; do not read every spec file for every question.

## Open design questions (not yet decided)

- Controlled topic tag vocabulary: deferred until retrieval fails.
  See Maps / tag-index, to be created only then.
- Daily note format: decided 2026-11-04, one file per day, hard-coded
  — see spec/creation.md, Daily notes. No longer an open question.
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
