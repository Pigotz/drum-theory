# Feature Landscape

**Domain:** Interactive educational presentation (Slidev / Web Audio / Music Notation)
**Researched:** 2026-04-19
**Sources:** Context7 (Slidev official docs, VexFlow GitHub, Tone.js GitHub) — HIGH confidence

---

## Table Stakes

Features the presentation cannot work without. Missing any of these = the talk fails.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Presenter notes on every slide | 12-minute hard cap demands timing cues; Slidev renders notes in presenter view (separate browser window) | Low | Use `<!-- note -->` comment blocks at end of each slide. Markdown supported. |
| Presenter timer | 12-minute cap with Act 1 as the biggest timing risk; Slidev supports `timer: countdown` + `duration: 12min` in frontmatter | Low | Built into Slidev presenter mode — zero implementation cost. Set `duration: 12min` and `timer: countdown`. |
| `v-click` step reveals on note-duration slides | Audience must see symbol *before* the analogy lands — simultaneous reveal kills the effect | Low | Native Slidev directive. Works on any element. Modifiers: `.fade`, `.scale`, `.none`. |
| `v-click` volunteer role reveal (Act 4) | Roles must appear one at a time — all visible at once spoils the orchestration gag | Low | Same mechanism, same zero cost. |
| Color-coded notation (kick=red, snare=blue, hi-hat=yellow) | Central learning device; audience maps color to limb throughout all acts | Low (with VexFlow) / Zero (with screenshots) | VexFlow: `staveNote.setStyle({ fillStyle: 'red', strokeStyle: 'red' })` per note. Screenshot: CSS overlay or pre-colored image. |
| Drumless backing track player (looping) | Act 4 Human Drum collapses without music; no other mechanism exists | Low | `<audio loop>` + Vue ref toggle. HTMLAudioElement — no library required. MP3 in `public/`. |
| GitHub Pages deployment | Sharing link with friends is the delivery mechanism; local-only = no audience | Low | `base: /drum-theory/` in frontmatter is the critical gotcha. GitHub Actions workflow is well-documented. |

---

## Differentiators

Features that make this talk memorable beyond what a static PowerPoint delivers.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| VexFlow static drum notation (replaces screenshots) | Notation is color-coded, crisp at any screen size, and maintainable as props — screenshots degrade and require re-export on every tweak | Medium | Requires percussion clef (`addClef('percussion')`), x-noteheads via `StaveNote` `type` field, two-voice layout (hi-hat stems up, kick/snare stems down via `stem_direction`), `setStyle()` per note for color. Wrapped in `DrumStaff.vue`. |
| Playable notation with beat cursor (Tone.js) | Audience hears the groove *while* watching the cursor sweep — closes the gap between abstract notation and felt rhythm; uniquely powerful for a music education context | Medium-High | `Tone.Transport` drives scheduling. `Tone.Player` per drum sound (kick, snare, hi-hat WAV files in `public/samples/`). Beat cursor = SVG overlay positioned by `Transport.scheduleRepeat` on each 8th-note tick. CRITICAL: `Tone.start()` must be called inside a user gesture (button click) — browser autoplay policy enforces this without exception. Extend `DrumStaff.vue` into `PlayableDrumStaff.vue`. |
| Single shared data model (notation + audio) | Pattern array drives both VexFlow rendering and Tone.js event scheduling — no drift between what's shown and what's heard | Medium | Design the beat data structure first (array of `{ instrument, beat, subdivision }` objects). VexFlow maps positions from this; Tone.js maps timing from this. |
| Slide overview visible during rehearsal | Slidev's `/overview` route shows all slides + speaker notes in one scrollable view — useful for rehearsing timing before the event | Zero | Built-in. No implementation. Notes editor at `/editor`. |

---

## Anti-Features

Things to deliberately not build. Each has an explicit reason.

| Anti-Feature | Why Avoid | What to Do Instead |
|--------------|-----------|-------------------|
| Audience remote control / live voting | Out of scope per PROJECT.md; audience interactivity is physical (Human Drum), not digital | Physical participation is the point — keep it analogue |
| Full drum machine / step sequencer UI | Massively out of scope; turns a 12-minute talk into a product | The playable notation component is the ceiling — it demonstrates one groove, not a composable interface |
| MIDI or Web MIDI input | No MIDI instruments present; adds browser permission complexity for zero gain | WAV samples via Tone.Player are sufficient |
| Offline export with audio (PDF/PPTX) | Slidev exports slides as images; audio cannot embed in PDF export — do not pursue | Presentation is always delivered live from the browser |
| Mobile-responsive layout | Audience watches on a projected screen; presenter controls from a laptop | Design for 16:9 landscape only |
| Waveform visualiser / frequency analyser | Visual flair that distracts from notation; adds canvas complexity | Color-coded cursor on the stave is sufficient visual feedback |
| Multiple simultaneous grooves / layering | Scope creep — one groove at a time is the pedagogical model | The three grooves (basic rock, double kick variation, chaos round) are played sequentially, not simultaneously |
| Recording or screen capture built-in | Slidev has `slidev export` for static capture; recording the live session is a separate concern | Use OBS or QuickTime externally if needed |

---

## Feature Dependencies

```
Drumless backing track player (LoopPlayer.vue)
  └── No dependencies — standalone HTMLAudioElement

VexFlow static notation (DrumStaff.vue)
  └── Requires: beat data structure definition (drives prop API)
  └── Requires: percussion clef knowledge + two-voice layout understanding

Playable notation (PlayableDrumStaff.vue)
  └── Requires: DrumStaff.vue (extends it)
  └── Requires: beat data structure (same model, reused)
  └── Requires: Tone.Transport + Tone.Player initialized after user gesture
  └── Requires: drum WAV samples in public/samples/
  └── Beat cursor requires: SVG coordinate knowledge of VexFlow note positions

GitHub Pages deployment
  └── Requires: base: /drum-theory/ in frontmatter (without this, all assets 404)
```

---

## Presenter Mode — What Matters

Slidev's presenter mode is a second browser window synced to the audience window. Key features relevant to this talk:

- **Speaker notes** — Markdown rendered in presenter view. The brief already specifies timing cues per slide (e.g. "Clap the quarter note 4 times. Ask them to join. ~30 seconds.").
- **Presentation timer** — Configurable as countdown (`timer: countdown`, `duration: 12min`). Shows progress bar. Start/pause/reset available. Zero implementation cost — set in frontmatter.
- **Slide sync** — Advancing slides in presenter view syncs all open audience windows. Reliable for single-presenter single-screen setup.
- **Notes editor** — `/editor` route provides a bulk-edit view of all notes. Useful during slide authoring.
- **Overview** — `/overview` route shows all slides + notes linearly. Good for rehearsal.

The timer is the highest-value presenter feature for this talk given the hard 12-minute cap and the explicit risk that Act 1 clapping demos can run long.

---

## MVP Recommendation

**Phase 1 — Must ship (talk works):**
1. All 13 slides with content and `v-click` animations
2. Presenter notes on every slide with timing cues
3. `timer: countdown` + `duration: 12min` in frontmatter
4. `LoopPlayer.vue` — backing track for Act 4
5. GitHub Pages deployment with correct `base:` path

**Phase 2 — Significant upgrade (talk looks polished):**
6. `DrumStaff.vue` — VexFlow static notation with color coding, replacing screenshots
   - Percussion clef, x-noteheads, two-voice layout, `setStyle()` per note

**Phase 3 — If time allows (talk becomes memorable):**
7. `PlayableDrumStaff.vue` — Tone.js audio + beat cursor on notation slides
   - Must call `Tone.start()` in button click handler (browser autoplay policy — no exceptions)
   - Shared beat data structure drives both VexFlow and Tone.js

Cut Phase 3 entirely if timeline is tight. The live Human Drum session in Act 4 already provides the "playable" experience for the audience.

---

## Sources

- Slidev presenter mode and timer: Context7 `/websites/sli_dev` — HIGH confidence
- Slidev `v-click` animations and component model: Context7 `/websites/sli_dev` — HIGH confidence
- VexFlow note styling (`setStyle`, `setKeyStyle`): Context7 `/0xfe/vexflow` (wiki/Coloring-&-Styling-Notes) — HIGH confidence
- VexFlow `StaveNoteStruct` `type` field and stem direction: Context7 `/0xfe/vexflow` (api/interfaces) — HIGH confidence
- Tone.js Transport scheduling and `Tone.start()` autoplay requirement: Context7 `/tonejs/tone.js` (wiki/Autoplay, README) — HIGH confidence
- Tone.js drum Player setup: Context7 `/tonejs/tone.js` (examples/shiny.html, examples/daw.html) — HIGH confidence
- VexFlow percussion clef support: inferred from `addClef` API accepting clef type strings; percussion-specific config confirmed referenced in project brief — MEDIUM confidence (direct percussion clef example not found in Context7 snippets; API structure confirms it is a valid clef type)
