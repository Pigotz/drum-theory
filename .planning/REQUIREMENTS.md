# Requirements: Drum Theory Presentation

**Defined:** 2026-04-19
**Core Value:** The audience walks away having actually played a drum groove together — the theory isn't the point, the experience is.

---

## v1 Requirements

### Slides

- [ ] **SLIDE-01**: Presentation has all 13 slides covering all 4 acts (cover, hook, 5 note duration slides, key insight, drum notation diagram, drum notation quote, volunteer roles, groove grid, outro)
- [ ] **SLIDE-02**: Note duration slides (slides 3–7) use v-click reveals — symbol shown first, pizza analogy revealed on click
- [ ] **SLIDE-03**: Act 4 volunteer role cards (kick/snare/hi-hat) revealed one at a time via v-click
- [ ] **SLIDE-04**: Presenter notes on every slide with timing cues and spoken prompts (visible in Slidev presenter view)
- [ ] **SLIDE-05**: Countdown timer configured (12 minutes) visible in presenter view

### Notation

- [ ] **NOTE-01**: Drum notation diagram (slide 9) uses color-coded staff — hi-hat yellow, snare blue, kick red — consistently applied throughout
- [ ] **NOTE-02**: DrumStaff.vue component renders VexFlow SVG percussion notation with correct percussion clef and x-noteheads for hi-hat
- [ ] **NOTE-03**: DrumStaff.vue supports two-voice layout — hi-hat stems up, kick stems down, formatted together
- [ ] **NOTE-04**: Groove grid on Act 4 slide uses HTML color grid (kick=red, snare=blue, hi-hat=yellow) to show beat patterns

### Audio

- [ ] **AUDIO-01**: LoopPlayer.vue component plays a drumless backing track on loop during Act 4 Human Drum slide
- [ ] **AUDIO-02**: LoopPlayer play/pause is controlled by a button click (no autoplay — browser gesture required)

### Deployment

- [x] **DEPLOY-01**: Presentation builds successfully with correct base path (`/drum-theory/`) — no 404s for assets on GitHub Pages
- [ ] **DEPLOY-02**: Deployed presentation accessible via GitHub Pages URL (manual deploy)

---

## v2 Requirements

### Audio Enhancement

- **AUDIO-03**: PlayableDrumStaff.vue — click-to-play audio for each notation slide via Tone.js with drum samples
- **AUDIO-04**: Beat cursor sweeps across the VexFlow stave in sync with Tone.js playback

### Automation

- **DEPLOY-03**: GitHub Actions auto-deploy on push to main (`deploy.yml`)

### Presenter Experience

- **PRES-01**: Short video clips or GIFs of the presenter playing each example beat embedded in slides

---

## Out of Scope

| Feature | Reason |
|---------|--------|
| Audience remote participation via devices | Live Human Drum session is physical — digital tooling adds complexity without value |
| Step sequencer UI | Not needed; patterns are fixed per slide |
| MIDI input | Out of scope for a presentation tool |
| Mobile layout optimization | Slidev is a laptop/projector tool |
| Multiple simultaneous groove patterns | One groove per slide is the teaching model |
| Waveform visualizer | No user value for this context |
| PDF export with audio | Export is out of scope; live presentation only |

---

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| SLIDE-01 | Phase 1 | Pending |
| SLIDE-02 | Phase 1 | Pending |
| SLIDE-03 | Phase 1 | Pending |
| SLIDE-04 | Phase 1 | Pending |
| SLIDE-05 | Phase 1 | Pending |
| NOTE-04 | Phase 1 | Pending |
| AUDIO-01 | Phase 1 | Pending |
| AUDIO-02 | Phase 1 | Pending |
| DEPLOY-01 | Phase 1 | Complete (01-01) |
| DEPLOY-02 | Phase 1 | Pending |
| NOTE-01 | Phase 2 | Pending |
| NOTE-02 | Phase 2 | Pending |
| NOTE-03 | Phase 2 | Pending |

**Coverage:**
- v1 requirements: 13 total
- Mapped to phases: 13
- Unmapped: 0 ✓

---

*Requirements defined: 2026-04-19*
*Last updated: 2026-04-19 after roadmap creation*
