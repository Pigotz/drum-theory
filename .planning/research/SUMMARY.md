# Project Research Summary

**Project:** You Can't Read This — Drum Theory Presentation
**Domain:** Interactive educational Slidev presentation with music notation and audio
**Researched:** 2026-04-19
**Confidence:** HIGH (stack, lifecycle, deployment) / MEDIUM (VexFlow percussion API specifics)

---

## Executive Summary

Three-phase progressive build: static slides (Phase 1) → VexFlow drum notation (Phase 2) → Tone.js playable notation (Phase 3). Treat Phase 3 as a cut feature from day one — the live Human Drum session provides the "interactive" moment regardless. Ship Phase 1 early, get GitHub Pages working immediately, then layer in components once slide content is locked.

Stack: Slidev `52.14.2` + VexFlow `5.0.0` + Tone.js `15.1.22`. The critical architectural decision is the `DrumPattern` interface — one boolean array per instrument drives both VexFlow rendering and Tone.js scheduling. Three components cover full scope: `LoopPlayer.vue` (no deps), `DrumStaff.vue` (VexFlow), `PlayableDrumStaff.vue` (adds Tone.js).

Top risks: (1) timeline — Phase 3 audio sync is routinely underestimated; (2) a cluster of silent-failure gotchas (base path, wrong lifecycle hook, AudioContext gate) each costing 30–60 min if not anticipated.

---

## Stack

| Library | Pin To | Reason |
|---------|--------|--------|
| `@slidev/cli` | `52.14.2` | Current stable (0.x → 52.x version scheme change June 2025) |
| `@slidev/theme-seriph` | `0.25.0` | Project requirement |
| `vexflow` | `5.0.0` | ESM-first; percussion confirmed supported |
| `tone` | `15.1.22` | npm `latest` stable; avoid `next` (`15.5.x`) |

---

## Table Stakes

- **Presenter notes + countdown timer** — `<!-- note -->` blocks + `timer: countdown` / `duration: 12min` in frontmatter. Zero cost.
- **`v-click` reveals** — Note-duration and Act 4 volunteer slides. Native Slidev directive.
- **Color-coded notation** — kick=red, snare=blue, hi-hat=yellow throughout all acts.
- **`LoopPlayer.vue` backing track** — Act 4 collapses without music. `<audio loop>` + Vue ref toggle.
- **GitHub Pages deploy** — The sharing link IS the delivery mechanism.

---

## Differentiators

- **`DrumStaff.vue`** — VexFlow SVG notation vs screenshots. Crisp at any size, color-coded, maintained as props.
- **`PlayableDrumStaff.vue`** — Beat cursor + Tone.js audio. High value, high risk. Optional.
- **Single data model** — `DrumPattern` drives both rendering and audio. No drift between what's shown and heard.

**Out of scope:** audience remote control, step sequencer UI, MIDI input, mobile layout, waveform visualizer.

---

## Architecture

```
slides.md
components/
  LoopPlayer.vue           ← HTML5 <audio>, no deps
  DrumStaff.vue            ← VexFlow SVG, DrumPattern prop
  PlayableDrumStaff.vue   ← DrumStaff + Tone.js + beat cursor
public/
  backing.mp3
  samples/{kick,snare,hihat}.wav
.github/workflows/deploy.yml
```

**DrumPattern interface** (single source of truth):
```typescript
interface DrumPattern {
  steps: number
  bpm: number
  hihat: boolean[]   // true=hit, false=rest
  snare: boolean[]
  kick:  boolean[]
}
```

No Pinia. No composables. No shared global state. Runtime audio paths via `import.meta.env.BASE_URL`.

---

## Build Order

1. **slides.md skeleton + frontmatter** — All 13 slides, `v-click` animations, presenter timer
2. **GitHub Pages deploy** — Wire immediately; base path is the most dangerous external integration
3. **`LoopPlayer.vue`** — Proves component pipeline; zero risk
4. **`DrumStaff.vue`** — Requires VexFlow percussion spike first; locks `DrumPattern` interface
5. **`PlayableDrumStaff.vue`** — Depends on stable interface + samples in `public/`; hard cut 48h before presentation

---

## Watch Out For

1. **Base path mismatch** — Use official Slidev GitHub Actions workflow verbatim. `base: /drum-theory/` in frontmatter. `import.meta.env.BASE_URL` for Tone.js Player paths (bare `/samples/kick.wav` 404s under subdirectory).
2. **`onMounted` vs `onSlideEnter`** — Slidev preserves instances across slides; `onMounted` doesn't fire on re-entry. Use `onSlideEnter`/`onSlideLeave` from `@slidev/client` for VexFlow re-render and Tone stop-on-leave.
3. **VexFlow percussion config** — Must set `addClef('percussion')`, `noteType: 'x'` for hi-hat, manual staff positions. V3/v4 tutorials don't apply to v5. Spike before writing component API.
4. **Tone.js AudioContext suspended** — `await Tone.start()` must be the first line of every audio click handler. Works in dev, silently fails on fresh demo page load.
5. **Phase 3 scope creep** — Hard cut point: if not working 48h before presentation, drop it. Static staves are already superior to screenshots.

---

## Open Questions (Empirical — Must Verify in Spike)

1. VexFlow v5 x-notehead call signature (`noteType: 'x'`? duration suffix? enum?)
2. Staff position strings for percussion clef (`hihat: 'a/5'`? — approximate)
3. `addClef('percussion')` renders correctly in v5?
4. `Tone.Draw` vs `Tone.getDraw()` in `tone@15.1.22`?
5. `note.getAbsoluteX()` accessible after `vf.draw()` inside Slidev lifecycle?
6. `import.meta.env.BASE_URL` works correctly in Slidev's Vite config for Tone.Player?

---

## Suggested Roadmap Shape

**3 coarse phases:**
1. Slide Skeleton + Deploy + Backing Track
2. VexFlow Static Notation (`DrumStaff.vue`)
3. Playable Notation (`PlayableDrumStaff.vue`) — optional, hard cut decision built in

---

*Research completed: 2026-04-19 | Ready for roadmap: yes*
