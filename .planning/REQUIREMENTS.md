# Requirements: Drum Theory Presentation

**Defined:** 2026-04-19
**Core Value:** The audience walks away having actually played a drum groove together — the theory isn't the point, the experience is.

## v2.0 Requirements

Requirements for the Polish & Aesthetics milestone. Each maps to roadmap phases.

### Theme & Background

- [ ] **THEME-01**: All 13 slides use a dark background with no white bleed-through
- [ ] **THEME-02**: Slidev theme or custom CSS produces a consistent dark palette globally

### SVG Note Symbols

- [ ] **SVG-01**: Note symbols on slides 3–7 (whole, half, quarter, eighth, sixteenth) are visible on dark backgrounds
- [ ] **SVG-02**: Note symbols maintain correct proportions and visual quality after dark-mode fix

### Typography

- [ ] **TYPE-01**: Slide headings use a sharp, bold typeface aligned with the Dribbble visual references
- [ ] **TYPE-02**: Body text and labels are legible and on-brand across all slides

### Color Palette

- [ ] **COL-01**: Kick=red (#ef4444), snare=blue (#3b82f6), hi-hat=yellow (#eab308) are consistent across slides, groove grid, and VexFlow notation
- [ ] **COL-02**: Accent/highlight colors used in slide design do not clash with the notation color palette

### VexFlow Compatibility

- [ ] **VEX-01**: DrumStaff.vue stave lines, noteheads, and stems all render visibly on dark background
- [ ] **VEX-02**: Per-notehead color coding (red/blue/yellow) is preserved and correct after theme change

### Layout & Polish

- [ ] **LAY-01**: Slide spacing and alignment is consistent and intentional across all 13 slides
- [ ] **LAY-02**: Act 4 groove grid is visually polished and readable on dark background
- [ ] **LAY-03**: LoopPlayer.vue button is styled to match the dark aesthetic

## Future Requirements

Deferred from v1.0 — not in v2.0 scope.

### Audio & Interactivity

- **AUDIO-01**: PlayableDrumStaff.vue — click-to-play audio per notation slide via Tone.js
- **AUDIO-02**: Beat cursor sweeps across VexFlow stave in sync with Tone.js playback

### Automation

- **AUTO-01**: GitHub Actions auto-deploy on push to main

### Presenter Media

- **MEDIA-01**: Short video clips or GIFs of presenter playing each beat, embedded in slides

## Out of Scope

| Feature | Reason |
|---------|--------|
| Audience remote participation | Live Human Drum session is physical — digital tooling adds complexity without value |
| Step sequencer UI | Not needed; patterns are fixed per slide |
| MIDI input | Out of scope for a presentation tool |
| Mobile layout optimization | Slidev is a laptop/projector tool |
| Multiple simultaneous groove patterns | One groove per slide is the teaching model |
| Waveform visualizer | No user value for this context |
| PDF export with audio | Export is out of scope; live presentation only |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| THEME-01 | Phase 3 | Pending |
| THEME-02 | Phase 3 | Pending |
| SVG-01 | Phase 3 | Pending |
| SVG-02 | Phase 3 | Pending |
| TYPE-01 | Phase 3 | Pending |
| TYPE-02 | Phase 3 | Pending |
| COL-01 | Phase 3 | Pending |
| COL-02 | Phase 3 | Pending |
| VEX-01 | Phase 3 | Pending |
| VEX-02 | Phase 3 | Pending |
| LAY-01 | Phase 3 | Pending |
| LAY-02 | Phase 3 | Pending |
| LAY-03 | Phase 3 | Pending |

**Coverage:**
- v2.0 requirements: 13 total
- Mapped to phases: 13
- Unmapped: 0 ✓

---
*Requirements defined: 2026-04-19*
*Last updated: 2026-04-19 after v2.0 milestone init*
