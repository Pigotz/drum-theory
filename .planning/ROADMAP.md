# Roadmap: Drum Theory Presentation

## Overview

Two-phase build for the "You Can't Read This" Slidev presentation. Phase 1 delivers everything needed for a live Human Drum session: all 13 slides with content, animations, presenter tools, the backing track player, and a working GitHub Pages deployment. Phase 2 layers in VexFlow drum notation via DrumStaff.vue, elevating the visual teaching slides from color grids to proper percussion staves. PlayableDrumStaff.vue (Tone.js audio + beat cursor) is v2 scope — cut if time runs short.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [x] **Phase 1: Slide Skeleton + Backing Track + Deploy** - All 13 slides authored, LoopPlayer.vue working, deployed to GitHub Pages
- [x] **Phase 2: VexFlow Drum Notation** - DrumStaff.vue renders color-coded percussion staves in place of color grids

## Phase Details

### Phase 1: Slide Skeleton + Backing Track + Deploy
**Goal**: A complete, deployable presentation that can run a live Human Drum session
**Depends on**: Nothing (first phase)
**Requirements**: SLIDE-01, SLIDE-02, SLIDE-03, SLIDE-04, SLIDE-05, NOTE-04, AUDIO-01, AUDIO-02, DEPLOY-01, DEPLOY-02
**Success Criteria** (what must be TRUE):
  1. Opening the GitHub Pages URL shows the full presentation with all 13 slides across all 4 acts — no 404s, no broken assets
  2. Note duration slides (3–7) reveal their pizza analogy on click; Act 4 volunteer role cards reveal one at a time on click
  3. The backing track plays and loops when the button is clicked on the Human Drum slide; it does not autoplay
  4. Presenter view shows speaker notes with timing cues on every slide and a 12-minute countdown timer
  5. The Act 4 groove grid slide renders an HTML color grid with kick=red, snare=blue, hi-hat=yellow beat patterns
**Plans**: 5 plans
**UI hint**: yes

Plans:
- [x] 01-01-PLAN.md — Scaffold Slidev project, configure base path, wire deploy scripts
- [x] 01-02-PLAN.md — Create SVG note symbols, silent MP3 placeholder, drum-sheet placeholder
- [x] 01-03-PLAN.md — Create LoopPlayer.vue backing track component
- [x] 01-04-PLAN.md — Author all 13 slides with content, v-click, groove grid, and speaker notes
- [x] 01-05-PLAN.md — Build and deploy to GitHub Pages, verify live URL

**Research notes:**
- Wire GitHub Pages deploy immediately after scaffold — base path (`/drum-theory/`) is the riskiest external integration; silent 404s if misconfigured
- Use official Slidev GitHub Actions workflow verbatim; `import.meta.env.BASE_URL` for any asset paths
- Use `onSlideEnter`/`onSlideLeave` from `@slidev/client` for component lifecycle — `onMounted` does not re-fire on slide re-entry
- Tone.js AudioContext: `await Tone.start()` must be first line of every click handler

### Phase 2: VexFlow Drum Notation
**Goal**: DrumStaff.vue renders proper VexFlow SVG percussion notation with color coding, replacing the color-grid placeholder
**Depends on**: Phase 1
**Requirements**: NOTE-01, NOTE-02, NOTE-03
**Success Criteria** (what must be TRUE):
  1. The drum notation diagram slide (slide 9) shows a VexFlow SVG stave with percussion clef and correctly positioned noteheads — hi-hat x-noteheads in yellow, snare in blue, kick in red
  2. Hi-hat stems point up and kick stems point down, correctly formatted as a two-voice layout on a single stave
  3. The color coding (kick=red, snare=blue, hi-hat=yellow) is consistent across all slides that display notation
**Plans**: 2 plans
**UI hint**: yes

Plans:
- [x] 02-01-PLAN.md — Install VexFlow 5.0.0 and run percussion spike (visual rendering validation)
- [x] 02-02-PLAN.md — Implement DrumStaff.vue and migrate slide 9 to two-cols layout

**Research notes:**
- Run a VexFlow percussion spike BEFORE writing the DrumStaff.vue API — v5 x-notehead call signature, staff positions, and `addClef('percussion')` rendering must be verified empirically; v3/v4 tutorials do not apply
- Open questions to resolve in spike: `noteType: 'x'` call signature, staff position strings (`a/5'`?), `addClef('percussion')` in v5, `note.getAbsoluteX()` accessibility after `vf.draw()` inside Slidev lifecycle
- Pin VexFlow to `5.0.0`; lock `DrumPattern` interface before building component API

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Slide Skeleton + Backing Track + Deploy | 5/5 | Complete | 2026-04-19 |
| 2. VexFlow Drum Notation | 2/2 | Complete | 2026-04-19 |
