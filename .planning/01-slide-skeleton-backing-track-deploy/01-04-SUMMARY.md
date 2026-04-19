---
phase: 01-slide-skeleton-backing-track-deploy
plan: "04"
subsystem: slides
tags: [slidev, content-authoring, v-click, presenter-notes, groove-grid]
dependency_graph:
  requires: [01-02, 01-03]
  provides: [slides.md-complete]
  affects: [01-05]
tech_stack:
  added: []
  patterns:
    - "Slidev two-cols layout with v-click wrapping right column only"
    - "Sequential v-click reveals for volunteer role cards"
    - "Inline HTML table for static groove grid with domain color coding"
    - "HTML comment blocks as presenter notes (last element in each slide block)"
key_files:
  created: []
  modified:
    - slides.md
decisions:
  - "D-11 honored: slide 1 cover has title + subline only — no presenter name"
  - "D-07 honored: volunteer role cards are color block divs (not plain text) — kick=#ef4444, snare=#3b82f6, hi-hat=#eab308"
  - "D-08 honored: groove grid shows Groove 1 only — no Groove 2"
  - "D-09 honored: Act 3 transition line folded into slide 10 (no 14th slide)"
  - "D-10 honored: slide 13 outro is plain text only — no QR code"
  - "D-12 honored: full spoken script with timing cues on every slide"
  - "Slide 8 uses center layout (not statement) — plan spec says center, UI-SPEC confirms center for display-size single sentence"
  - "v-click count: 8 open tags — 5 for note slides (3-7) + 3 for volunteer cards (slide 11)"
metrics:
  duration: "8 min"
  completed: "2026-04-19"
  tasks_completed: 2
  files_modified: 1
---

# Phase 1 Plan 04: Slide Content Authoring Summary

Complete authoring of all 13 slides in slides.md — cover through outro, with pizza analogies, v-click reveals, color-coded volunteer cards, groove grid, LoopPlayer reference, and speaker notes on every slide.

## What Was Built

All 13 placeholder stubs replaced with full slide content across 4 acts:

**Act 1 — Note Durations (slides 1-7):**
- Slide 1 (cover): title + subline, no presenter name
- Slide 2 (image): full-screen drum sheet hook
- Slides 3-7 (two-cols): each note (whole/half/quarter/eighth/sixteenth) with SVG symbol + note name in left column (always visible), pizza analogy wrapped in `<v-click>` in right column (revealed on presenter click)

**Act 2 — Drum Notation (slides 8-10):**
- Slide 8 (center): key insight at 48px display size — "The beat doesn't speed up. You just fit more sounds inside the same time."
- Slide 9 (image-right): color-coded bullet list mapping staff position to drum instrument; right panel scaffolded with placeholder drum-sheet image
- Slide 10 (statement): notation quote + Act 3 transition line folded in (no separate 14th slide)

**Act 4 — Human Drum Session (slides 11-13):**
- Slide 11 (default): three sequential v-click color block cards — kick (red), snare (blue), hi-hat (yellow) — revealed one per presenter click
- Slide 12 (two-cols): static HTML groove grid (Groove 1: HH all 8 cells, SN beats 2+4, KK beats 1+3) left column; LoopPlayer component right column
- Slide 13 (end): "Thanks for being my drum kit."

**Speaker notes:** Full spoken script with timing cues on every slide (13 comment blocks total).

## Tasks Completed

| Task | Name | Commit | Files |
|------|------|--------|-------|
| 1 | Author slides 1-7 (Cover, Hook, Note duration slides) | 100995d | slides.md |
| 2 | Author slides 8-13 (Key insight through Outro) | 100995d | slides.md |

Note: Both tasks were written atomically in one Write operation; single commit covers both.

## Verification Results

| Check | Expected | Actual | Pass |
|-------|----------|--------|------|
| `grep -c "^layout:" slides.md` | 13 | 13 | PASS |
| `grep -c "<v-click>" slides.md` | 8 | 8 | PASS |
| `grep "LoopPlayer" slides.md` | `<LoopPlayer />` | `<LoopPlayer />` | PASS |
| Color lines `#eab308\|#3b82f6\|#ef4444` | >= 6 | 18 | PASS |
| `grep -c "<!--" slides.md` | >= 13 | 13 | PASS |
| `npm run build` | exit 0 | exit 0 | PASS |
| `layout: cover` present | yes | yes | PASS |
| `layout: image` present | yes | yes | PASS |
| `layout: center` present (slide 8) | yes | yes | PASS |
| `layout: image-right` present (slide 9) | yes | yes | PASS |
| `layout: statement` present (slide 10) | yes | yes | PASS |
| `layout: end` present (slide 13) | yes | yes | PASS |
| `Chief Kick Officer` copy | present | present | PASS |
| `Snare Department Head` copy | present | present | PASS |
| `Hi-Hat Technician` copy | present | present | PASS |
| `Thanks for being my drum kit.` | present | present | PASS |
| `The beat doesn't speed up` | present | present | PASS |
| `This isn't a melody` | present | present | PASS |
| Slide 7 speaker note has "skip"/"escape" | yes | yes | PASS |
| timer: countdown in headmatter | yes | yes | PASS |
| 5 SVG src paths `/svgs/` | 5 | 5 | PASS |

## Deviations from Plan

None — plan executed exactly as written.

Both tasks written in one atomic Write operation (single file, complete content) and verified by build exit 0. All acceptance criteria met for both Task 1 and Task 2 before the single commit.

## Known Stubs

| Stub | File | Line | Reason |
|------|------|------|--------|
| `image: /drum-sheet.png` (slides 2, 9) | slides.md | 22, 130 | Placeholder CC image — user replaces with real drum sheet before live use (D-02, D-06) |
| `public/backing.mp3` (referenced by LoopPlayer) | slides.md | 198 | Silent placeholder — user replaces with real drumless backing track before live use (D-02) |

These stubs are intentional per locked decisions D-02 and D-06. They do not prevent the plan's goal (slide authoring) from being achieved — they are assets to be replaced by the user, documented in RESEARCH.md.

## Threat Flags

None. slides.md is static Markdown rendered in Slidev's controlled context — no user input, no dynamic data, no new network endpoints or trust boundaries introduced.

## Self-Check: PASSED

- slides.md: FOUND and modified (298 insertions)
- Commit 100995d: FOUND (`git log --oneline | head -1` confirms)
- Build: exit 0 confirmed
- All 13 layouts present, 8 v-click reveals, 13 comment blocks, `<LoopPlayer />` on slide 12
