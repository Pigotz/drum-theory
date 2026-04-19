# Drum Theory Presentation

## What This Is

A Slidev-powered presentation called *"You Can't Read This"* — a 12-minute PowerPoint night talk for close friends. The presenter (a drummer) teaches drum notation through pizza analogies and audience participation, culminating in a live "Human Drum" session where volunteers become a real groove.

## Core Value

The audience walks away having actually played a drum groove together — the theory isn't the point, the experience is.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Slidev presentation with 13 slides covering all 4 acts
- [ ] Presenter notes on every slide (timing cues, spoken prompts)
- [ ] Drumless backing track player component (looping MP3)
- [ ] VexFlow drum notation component (static staves, percussion clef, color-coded)
- [ ] Playable notation component with Tone.js audio + beat cursor
- [ ] GitHub Pages deployment via GitHub Actions

### Out of Scope

- Mobile app — web-first presentation tool
- Multi-language support — single English presentation
- Audience interactivity via their own devices — live Human Drum is physical, not digital

## Context

- **Audience:** Non-technical, music-theory-naive close friends at a PowerPoint night
- **Presenter:** A drummer — can explain feel and performance, not just theory
- **Format:** Slidev (Vue-based, Markdown-driven, supports custom components)
- **Time constraint:** Presentation has a hard 12-minute cap; Act 1 (note durations) is the biggest timing risk
- **Build order from brief:** (1) backing track player — quick win; (2) VexFlow static notation — visual foundation; (3) playable notation — audio layer on top
- **Deadline:** Soon — playable notation gets cut if time runs out

## Constraints

- **Tech stack:** Slidev + Vue 3 + VexFlow + Tone.js — no alternative frameworks
- **Hosting:** GitHub Pages under `/drum-theory/` subdirectory — base path must be set in frontmatter
- **Design:** Theme `seriph` (dark); color coding: kick = red, snare = blue, hi-hat = yellow; one concept per slide
- **Timeline:** Tight — prioritize in brief's stated build order

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Slidev over PowerPoint/Google Slides | Supports Vue components, code highlighting, presenter view, GitHub Pages deploy | — Pending |
| VexFlow for notation | Programmatic staves avoid screenshot maintenance; percussion-specific config possible | — Pending |
| Tone.js for audio | Web Audio scheduling with beat-accurate cursor sync; pairs naturally with VexFlow | — Pending |
| seriph theme | Dark background makes notation pop; matches drummer aesthetic | — Pending |
| Backing track player first | Lowest-effort feature, immediate demo value for Human Drum session | — Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-04-19 after initialization*
