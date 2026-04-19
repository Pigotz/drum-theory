# Milestones

## v1.0 — "You Can't Read This" MVP

**Shipped:** 2026-04-19
**Phases:** 1–2 | **Plans:** 7
**Requirements:** 13/13 complete

### Delivered

A complete, deployable Slidev presentation for a 12-minute PowerPoint night talk teaching drum notation via pizza analogies, culminating in a live Human Drum audience participation session. Ships with VexFlow percussion notation, a looping backing track player, and a live GitHub Pages deployment.

### Accomplishments

1. Slidev scaffold with correct base path (`/drum-theory/`) and GitHub Pages deploy pipeline
2. 5 hand-crafted SVG note symbols (whole → sixteenth) + LoopPlayer.vue backing track component
3. All 13 slides authored with v-click reveals, groove grid, and speaker notes with timing cues
4. VexFlow 5.0.0 percussion spike — confirmed x-notehead API (`'a/5/x'` syntax, `setKeyStyle` coloring)
5. DrumStaff.vue — two-voice VexFlow SVG percussion stave with per-notehead color coding
6. Slide 9 migrated from static image placeholder to live VexFlow notation

### Stats

- Timeline: 2026-04-19 (single session)
- Source LOC: ~196 (Vue/TS/JS)
- Key component: `components/DrumStaff.vue`

### Deferred to v2

- PlayableDrumStaff.vue (Tone.js audio + beat cursor)
- GitHub Actions auto-deploy
- Presenter video/GIF clips
