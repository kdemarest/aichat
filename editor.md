# editor — Requirements / Decision

## Purpose
Obsidian handles wrangling the game-content `.md` files well, but is poor
for viewing/editing code. Need a decent editor on Android that covers
that gap.

## Decision: install an existing app, don't build one
Building on Monaco or CodeMirror would mean solving mobile text-selection
UX ourselves (see prior discussion — neither is production-ready on
touch, Monaco especially). For an MVP, cheaper to install an existing
Android code editor that already meets the requirements below.

Candidate: **[Acode](https://acode.app)** — open source, on both F-Droid
and Play Store. Native Android text view (not a custom-rendered canvas),
so it should get OS-level tap/selection-handle behavior for free. Feature
list (syntax highlighting for 100+ languages, file browser, multi-file
tabs, search-in-all-files, find/replace) appears to cover v1 and v2
below out of the box.

## aichat scope, narrowed
`aichat` becomes just the chat window — like this session — nothing
more. No file browsing/editing responsibility. It's purely a client of
`aibridge`. Code editing happens in the separate editor app (Acode),
switched to manually. `aichat` and the editor both read/write the same
on-disk files independently; each must notice when the other (or
`claude`, via `aibridge`) has changed a file and reload it.

## Requirements checklist (mapped to Acode, pending hands-on verification)
- Open / save / close files — yes
- Tap to place cursor, drag start/end selection handles, cut/copy/paste
  — yes, standard native Android text editing
- Syntax highlighting — yes (bonus, was optional)
- Find in file — yes
- Auto-reload when a file changes on disk (e.g. `claude` edited it via
  `aibridge`) — **unverified, needs hands-on check**
- File browser — yes
- Rename file — yes
- Global search/replace (across files) — yes ("search in all files")
- Find/replace in file — yes
- Multiple tabs of open files — yes

## Open questions
- Verify Acode's actual behavior when an open file changes on disk
  externally: does it prompt to reload, silently reload, or risk
  overwriting the on-disk change on next save?
- F-Droid vs. Play Store build of Acode — any feature/permission
  differences worth knowing about?
