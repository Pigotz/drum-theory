# Drum Theory Presentation

## What This Is

A Slidev-powered presentation called *"You Can't Read This"* — a 12-minute PowerPoint night talk for close friends. The presenter (a drummer) teaches drum notation through pizza analogies and audience participation, culminating in a live "Human Drum" session where volunteers become a real groove. v1.0 shipped with all 13 slides, color-coded VexFlow percussion notation, and a looping backing track player.

## Core Value

The audience walks away having actually played a drum groove together — the theory isn't the point, the experience is.

## Requirements

### Validated

- ✓ All 13 slides covering all 4 acts (cover, hook, 5 note durations, key insight, notation, quote, volunteer roles, groove grid, outro) — v1.0
- ✓ v-click reveals on note duration slides (3–7): symbol first, pizza analogy on click — v1.0
- ✓ Act 4 volunteer role cards revealed one at a time via v-click — v1.0
- ✓ Presenter notes on every slide with timing cues and spoken prompts — v1.0
- ✓ 12-minute countdown timer in presenter view — v1.0
- ✓ Groove grid (Act 4) as HTML color grid: kick=red, snare=blue, hi-hat=yellow — v1.0
- ✓ LoopPlayer.vue — drumless backing track plays on loop during Act 4, button-triggered — v1.0
- ✓ Presentation builds with correct base path (`/drum-theory/`) — no 404s on GitHub Pages — v1.0
- ✓ Deployed to GitHub Pages, live URL verified — v1.0
- ✓ DrumStaff.vue — VexFlow SVG percussion stave with two-voice layout and per-notehead color coding — v1.0
- ✓ Slide 9 shows percussion clef + x-noteheads: hi-hat yellow, snare blue, kick red — v1.0
- ✓ Hi-hat stems up / kick stems down, correctly formatted as two-voice stave — v1.0

### Active

- [ ] PlayableDrumStaff.vue — click-to-play audio per notation slide via Tone.js with drum samples (v2)
- [ ] Beat cursor sweeps across the VexFlow stave in sync with Tone.js playback (v2)
- [ ] GitHub Actions auto-deploy on push to main — `deploy.yml` (v2)
- [ ] Short video clips or GIFs of presenter playing each beat, embedded in slides (v2)

### Out of Scope

| Feature | Reason |
|---------|--------|
| Audience remote participation via devices | Live Human Drum session is physical — digital tooling adds complexity without value |
| Step sequencer UI | Not needed; patterns are fixed per slide |
| MIDI input | Out of scope for a presentation tool |
| Mobile layout optimization | Slidev is a laptop/projector tool |
| Multiple simultaneous groove patterns | One groove per slide is the teaching model |
| Waveform visualizer | No user value for this context |
| PDF export with audio | Export is out of scope; live presentation only |

## Context

- **Audience:** Non-technical, music-theory-naive close friends at a PowerPoint night
- **Presenter:** A drummer — can explain feel and performance, not just theory
- **Format:** Slidev (Vue-based, Markdown-driven, supports custom components)
- **Time constraint:** Presentation has a hard 12-minute cap; Act 1 (note durations) is the biggest timing risk
- **v1.0 state:** ~196 LOC (Vue/TS/JS), shipped 2026-04-19. Tech stack: Slidev 52.14.2, VexFlow 5.0.0, Tone.js 15.1.22 (installed but not yet used in components)
- **Known issues:** None — spike resolved all VexFlow v5 percussion API questions before implementation
- **v2 focus:** PlayableDrumStaff.vue (Tone.js audio + beat cursor) — cut from v1.0 due to time

## Constraints

- **Tech stack:** Slidev + Vue 3 + VexFlow + Tone.js — no alternative frameworks
- **Hosting:** GitHub Pages under `/drum-theory/` subdirectory — base path must be set in frontmatter
- **Design:** Theme `seriph` (light background); color coding: kick = red, snare = blue, hi-hat = yellow; one concept per slide
- **VexFlow:** SVG backend only (not Canvas); all init inside `onMounted()`, guard null refs
- **Slidev lifecycle:** use `onSlideEnter`/`onSlideLeave` from `@slidev/client` — not `onMounted`/`onUnmounted`

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Slidev over PowerPoint/Google Slides | Supports Vue components, code highlighting, presenter view, GitHub Pages deploy | ✓ Good — worked seamlessly |
| VexFlow for notation | Programmatic staves avoid screenshot maintenance; percussion-specific config possible | ✓ Good — VexFlow 5.0.0 percussion API confirmed via spike |
| Tone.js for audio | Web Audio scheduling with beat-accurate cursor sync; pairs naturally with VexFlow | — Pending (v2 scope) |
| seriph theme | Dark aesthetic matches drummer vibe; makes notation pop | ⚠️ Revisit — theme is actually light background; removed global white ctx style that caused white-on-white stave |
| Backing track player first | Lowest-effort feature, immediate demo value for Human Drum session | ✓ Good — LoopPlayer.vue simple HTML5 audio, no Tone.js needed |
| Deploy first within Phase 1 | Base path `/drum-theory/` is highest-risk integration; validate early | ✓ Good — prevented silent 404s |
| VexFlow percussion spike before DrumStaff.vue API | v5 x-notehead API undocumented; spike confirmed `'a/5/x'` syntax and `setKeyStyle` coloring | ✓ Good — eliminated rework risk |
| Hi-hat beams in groups of 2 | Matches standard drum notation conventions | ✓ Good |
| Kick rests hidden via `fillStyle:'none'` | Reduces visual clutter without breaking voice tick count | ✓ Good |
| PlayableDrumStaff.vue deferred to v2 | Time constraint — static notation delivers core value; audio is enhancement | ✓ Good — right cut |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-04-19 after v1.0 milestone*
