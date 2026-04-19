---
phase: 01-slide-skeleton-backing-track-deploy
plan: 02
subsystem: ui
tags: [svg, audio, placeholder, static-assets]

# Dependency graph
requires:
  - phase: 01-slide-skeleton-backing-track-deploy/01-01
    provides: Slidev scaffold with public/ directory convention established
provides:
  - public/svgs/ directory with five hand-crafted SVG note symbol files (whole, half, quarter, eighth, sixteenth)
  - public/backing.mp3 silent placeholder for LoopPlayer.vue audio source
  - public/drum-sheet.png CC placeholder image for slide 2 hook
  - public/ASSETS-README.txt documenting replacement instructions for user
affects:
  - 01-04 (slides.md authoring — SVG img refs and drum-sheet.png img ref)
  - 01-03 (LoopPlayer.vue — backing.mp3 must exist in public/)

# Tech tracking
tech-stack:
  added: []
  patterns:
    - "Hand-crafted SVG note symbols using ellipse + line + path, viewBox 0 0 60 120, white stroke/fill for dark theme"
    - "Silent MP3 placeholder via curl download from anars/blank-audio GitHub repo"
    - "CC image placeholder from Wikimedia Commons"

key-files:
  created:
    - public/svgs/whole-note.svg
    - public/svgs/half-note.svg
    - public/svgs/quarter-note.svg
    - public/svgs/eighth-note.svg
    - public/svgs/sixteenth-note.svg
    - public/backing.mp3
    - public/drum-sheet.png
    - public/ASSETS-README.txt
  modified: []

key-decisions:
  - "SVG viewBox is 0 0 60 120 (portrait, allows stem room above notehead at cy=80)"
  - "Notehead at cy=80, stem from y=74 to y=10 — gives visual weight to bottom half while stem rises clearly"
  - "backing.mp3 sourced from anars/blank-audio (Option C) — avoids WAV/MP3 type confusion of Python wave approach"
  - "drum-sheet.png from Wikimedia Commons Drumkit_score.svg thumbnail — CC licensed, recognizable as notation"

patterns-established:
  - "Pattern: Static assets in public/ only — never src/ or components/ — for correct Vite build inclusion"
  - "Pattern: White stroke/fill on all SVG note symbols — renders correctly on seriph dark theme without CSS overrides"

requirements-completed:
  - SLIDE-02

# Metrics
duration: 2min
completed: 2026-04-19
---

# Phase 1 Plan 02: Static Assets Summary

**Five hand-crafted SVG note symbols (whole through sixteenth) plus silent MP3 and CC drum-sheet PNG placeholders — all in public/ for Vite build inclusion**

## Performance

- **Duration:** 2 min
- **Started:** 2026-04-19T09:54:40Z
- **Completed:** 2026-04-19T09:56:09Z
- **Tasks:** 2
- **Files modified:** 8

## Accomplishments
- Five SVG note symbol files created in public/svgs/ with correct anatomical structure: hollow/filled noteheads, upward stems, one or two curved flags
- Silent 1-second MP3 placeholder downloaded from anars/blank-audio (36KB, valid MP3, browser-loadable)
- CC drum notation PNG downloaded from Wikimedia Commons (2.1KB) as slide 2 hook placeholder
- ASSETS-README.txt documents both placeholders with user replacement instructions

## Task Commits

Each task was committed atomically:

1. **Task 1: Create five hand-crafted SVG note symbol files** - `770dc65` (feat)
2. **Task 2: Generate silent MP3 placeholder and drum-sheet placeholder image** - `d5e82d0` (feat)

**Plan metadata:** committed with docs commit below

## Files Created/Modified
- `public/svgs/whole-note.svg` - Hollow tilted ellipse, no stem
- `public/svgs/half-note.svg` - Hollow tilted ellipse with upward stem
- `public/svgs/quarter-note.svg` - Filled tilted ellipse with upward stem
- `public/svgs/eighth-note.svg` - Filled notehead, stem, single curved flag
- `public/svgs/sixteenth-note.svg` - Filled notehead, stem, two curved flags
- `public/backing.mp3` - Silent 1-second MP3 placeholder (user replaces before live use)
- `public/drum-sheet.png` - CC drum notation image placeholder (user replaces before live use)
- `public/ASSETS-README.txt` - Replacement instructions for both placeholder files

## Decisions Made
- viewBox `0 0 60 120` chosen (portrait) to give notehead at cy=80 with full stem height to y=10
- Stem x-position at x=47 (right side of ellipse) follows standard engraving convention
- Eighth/sixteenth flags use cubic bezier path sweeping right from stem top — drummer aesthetic, bold strokes
- anars/blank-audio Option C preferred over Python WAV approach to avoid browser MIME type mismatch

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None.

## User Setup Required

**User must replace placeholder assets before live presentation:**

1. `public/backing.mp3` — Replace with a real drumless backing track at ~90 BPM
   - Sources: YouTube "drumless" versions, Splice, GarageBand export
2. `public/drum-sheet.png` — Replace with an actual drum sheet screenshot
   - This is the full-screen opening visual on Slide 2 (Hook)

See `public/ASSETS-README.txt` for reminder text.

## Known Stubs

| Stub | File | Reason |
|------|------|--------|
| Silent placeholder audio | public/backing.mp3 | User replaces with real backing track before live use (D-02) |
| CC placeholder image | public/drum-sheet.png | User replaces with actual drum sheet screenshot before live use (D-02) |

Both stubs are intentional per decision D-02. Plan 01-02 goal (assets exist and don't 404) is fully achieved. Real content replacement is a user action, not a future plan task.

## Next Phase Readiness
- All SVG note symbols ready for `<img src="/svgs/*.svg">` references in slides.md (Plan 04)
- backing.mp3 ready for LoopPlayer.vue `:src` binding (Plan 03)
- drum-sheet.png ready for slide 2 `<img>` reference (Plan 04)
- No blockers for dependent plans

---
*Phase: 01-slide-skeleton-backing-track-deploy*
*Completed: 2026-04-19*
