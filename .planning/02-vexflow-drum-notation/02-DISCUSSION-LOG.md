# Phase 2: VexFlow Drum Notation - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-04-19
**Phase:** 02-vexflow-drum-notation
**Areas discussed:** Spike strategy, Slide 2 scope, Voice merging, Fallback plan, VexFlow dark background, VexFlow API style, Groove data location, Notation scope (NOTE-01), PlayableDrumStaff scope, VexFlow install & Vite compat, Error handling, Stave lines color, Slide 9 copy update

---

## Spike Strategy

| Option | Description | Selected |
|--------|-------------|----------|
| Dedicated spike task first | Throwaway spike proves x-notehead call sig, staff positions, addClef('percussion') empirically. DrumStaff.vue written only after validation. | ✓ |
| Fold into first implementation task | Experimentation folded into first impl task, findings documented inline. | |
| Skip spike, trust UI-SPEC estimates | Proceed directly on best-guess positions from UI-SPEC. | |

**User's choice:** Dedicated spike task first
**Notes:** ROADMAP.md already flagged this as required; user confirmed the dedicated-spike approach.

---

## Slide 2 Scope

| Option | Description | Selected |
|--------|-------------|----------|
| Leave as static image | Slide 2 is intentionally overwhelming — real drum sheet works better than clean VexFlow. User supplies the image. | ✓ |
| Replace with DrumStaff.vue | Consistent but defeats the 'overwhelming wall' purpose. | |
| Replace with busier DrumStaff | Multi-measure to maintain intimidation factor. | |

**User's choice:** Leave as static image
**Notes:** Slide 2's intent is to be visually overwhelming before the presenter simplifies it.

---

## Voice Merging Approach

| Option | Description | Selected |
|--------|-------------|----------|
| VexFlow chords (stacked noteheads) | Hi-hat + snare as chord at beats 2 & 4 within Voice 1. Simpler. | ✓ |
| Let spike decide | Spike determines the approach empirically. | |
| Three Voice objects | hi-hat = V1, snare = V2, kick = V3. Full per-note control. | |

**User's choice:** VexFlow chords (stacked noteheads)
**Notes:** Chord color granularity (setStyle per-notehead vs per-chord) is still an open question for the spike to resolve.

---

## Fallback Plan

| Option | Description | Selected |
|--------|-------------|----------|
| Hand-crafted SVG staff | Manually drawn SVG mimicking percussion stave. Guaranteed correct. | ✓ |
| Keep Phase 1 color-grid | No stave; bullets remain. Doesn't meet NOTE-01/02/03. | |
| No fallback — VexFlow must work | Spike surfaces blockers early enough to iterate. | |

**User's choice:** Hand-crafted SVG staff
**Notes:** Fallback is activated only if spike reveals VexFlow v5 percussion is broken or too complex.

---

## VexFlow Dark Background

| Option | Description | Selected |
|--------|-------------|----------|
| Global context override | context.setFillStyle/setStrokeStyle('white') before any drawing. Per-note setStyle() adds instrument colors on top. | ✓ |
| CSS filter: invert(1) | Flips stave to white but breaks instrument color contract. | |
| Per-element override only | Override every SVG element individually. Tedious and fragile. | |

**User's choice:** Global context override

---

## VexFlow API Style

| Option | Description | Selected |
|--------|-------------|----------|
| Low-level API | Renderer + Stave + Voice + Formatter + StaveNote. Full control. | ✓ |
| Factory API | Vex.Flow.Factory. Simpler but less control for percussion edge cases. | |

**User's choice:** Low-level API

---

## Groove Data Location

| Option | Description | Selected |
|--------|-------------|----------|
| Inline in the slide | <script setup> block in slides.md. Self-contained. | ✓ |
| Separate patterns.ts file | components/patterns.ts exporting named patterns. | |
| Hardcoded inside DrumStaff.vue | Built-in default pattern. Less flexible. | |

**User's choice:** Inline in the slide

---

## Notation Scope (NOTE-01)

| Option | Description | Selected |
|--------|-------------|----------|
| Slide 9 only | 'Throughout' means consistent colors across the app, not multiple DrumStaff instances. | ✓ |
| Slides 9 + additional | More DrumStaff instances across multiple slides. | |

**User's choice:** Slide 9 only
**Notes:** NOTE-01's "consistently applied throughout" refers to the color constants (red/blue/yellow), not to DrumStaff appearing on every slide.

---

## PlayableDrumStaff Scope

| Option | Description | Selected |
|--------|-------------|----------|
| Yes — strictly deferred | Phase 2 = static only. No Tone.js in Phase 2. | ✓ |
| Optional — include if time allows | Start PlayableDrumStaff.vue if time allows in Phase 2. | |

**User's choice:** Strictly deferred

---

## VexFlow Install & Vite Compatibility

| Option | Description | Selected |
|--------|-------------|----------|
| Standard install, verify ESM | npm install vexflow@5.0.0, confirm Vite ESM import. Add optimizeDeps.include if needed. Spike includes smoke test. | ✓ |
| Pin exact version and lock | Same but more conservative on version drift. | |
| Already installed — just import | Assume works out of the box. | |

**User's choice:** Standard install, verify ESM

---

## Error Handling

| Option | Description | Selected |
|--------|-------------|----------|
| console.error + silent fail | try/catch in onMounted. Log error, leave container empty. Presentation keeps running. | ✓ |
| Show a visible error state | Error message/border inside component. Good for dev, bad on projector. | |
| Throw to Vue error boundary | Let Vue catch it. Less controlled. | |

**User's choice:** console.error + silent fail

---

## Stave Lines Color

| Option | Description | Selected |
|--------|-------------|----------|
| Global context override handles it | context.setFillStyle/setStrokeStyle('white') covers stave lines, clef, barlines automatically. | ✓ |
| Explicit white per structural element | Per-element precision at the cost of verbosity. | |

**User's choice:** Global context override handles it

---

## Slide 9 Copy Update

| Option | Description | Selected |
|--------|-------------|----------|
| Bullets unchanged, update speaker note only | Left-column bullets stay identical. Only speaker note updated (remove placeholder reference). | ✓ |
| Update bullets to reference VexFlow diagram | Bullets say "see the staff on the right". Changes Phase 1 copy. | |

**User's choice:** Bullets unchanged, update speaker note only

---

## Claude's Discretion

- Exact VexFlow Formatter/Voice beam configuration for two-voice layout
- Whether optimizeDeps.include is needed (spike smoke-test determines this)
- Final staff position strings (confirmed by spike, may override UI-SPEC estimates)
- Chord color granularity (setStyle per-notehead vs per-chord — spike determines)

## Deferred Ideas

- PlayableDrumStaff.vue (Tone.js + beat cursor) — v2 requirement
- Slide 2 DrumStaff replacement — intentionally static
- Additional notation slides beyond slide 9
