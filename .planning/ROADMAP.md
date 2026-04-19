# Roadmap: Drum Theory Presentation

## Milestones

- ✅ **v1.0 MVP** — Phases 1–2 (shipped 2026-04-19)
- 🚧 **v2.0 Polish & Aesthetics** — Phase 3 (in progress)

## Phases

<details>
<summary>✅ v1.0 MVP (Phases 1–2) — SHIPPED 2026-04-19</summary>

- [x] Phase 1: Slide Skeleton + Backing Track + Deploy (5/5 plans) — completed 2026-04-19
- [x] Phase 2: VexFlow Drum Notation (2/2 plans) — completed 2026-04-19

Full archive: `.planning/milestones/v1.0-ROADMAP.md`

</details>

### 🚧 v2.0 Polish & Aesthetics (In Progress)

**Milestone Goal:** Redesign all 13 slides for a sharp, dark aesthetic — dark backgrounds, on-brand typography, consistent color palette, and a VexFlow stave that renders correctly in dark mode.

- [ ] **Phase 3: Visual Redesign** - Dark theme, typography, color palette, SVG symbols, and VexFlow dark-mode compatibility applied across all 13 slides

## Phase Details

### Phase 3: Visual Redesign
**Goal**: The presentation looks sharp and intentional — dark backgrounds, bold typography, consistent color coding, and all visual components (SVG symbols, VexFlow stave, groove grid, player button) render correctly and cohesively
**Depends on**: Phase 2
**Requirements**: THEME-01, THEME-02, SVG-01, SVG-02, TYPE-01, TYPE-02, COL-01, COL-02, VEX-01, VEX-02, LAY-01, LAY-02, LAY-03
**Success Criteria** (what must be TRUE):
  1. All 13 slides display a dark background with no white bleed-through in any browser or presenter view
  2. Note symbols on slides 3–7 are fully visible and correctly proportioned on the dark background
  3. Slide headings and body text use sharp, bold, on-brand typography throughout
  4. Kick=red, snare=blue, hi-hat=yellow are consistent across every slide, the groove grid, and the VexFlow stave
  5. DrumStaff.vue stave lines, noteheads, and stems are all visible on the dark background with per-notehead color coding intact
**Plans**: 3 plans

Plans:
- [ ] 03-01-PLAN.md — Create style.css (dark theme, Space Grotesk, groove grid, blend mode) + remove theme: seriph
- [ ] 03-02-PLAN.md — Update DrumStaff.vue (VexFlow dark-mode context fix) + LoopPlayer.vue (restyle + onSlideLeave cleanup)
- [ ] 03-03-PLAN.md — Visual QA across all 13 slides + production build + GitHub Pages deploy

**UI hint**: yes

## Progress

| Phase | Milestone | Plans Complete | Status | Completed |
|-------|-----------|----------------|--------|-----------|
| 1. Slide Skeleton + Backing Track + Deploy | v1.0 | 5/5 | Complete | 2026-04-19 |
| 2. VexFlow Drum Notation | v1.0 | 2/2 | Complete | 2026-04-19 |
| 3. Visual Redesign | v2.0 | 0/3 | Not started | - |
