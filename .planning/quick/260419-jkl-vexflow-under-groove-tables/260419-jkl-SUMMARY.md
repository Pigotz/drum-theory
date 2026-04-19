---
status: complete
---

# Quick Task 260419-jkl: VexFlow notation under groove tables (slides 14–19)

## What was done

- Created `components/GrooveDrumStaff.vue` — accepts the same `{ hh, sn, kk }` groove prop as GrooveTable and renders a live VexFlow percussion stave
- Updated slides 14–19: each `::left::` slot now stacks GrooveTable (flex: 1) above GrooveDrumStaff (fixed 150px height)
- Conversion logic handles: hihat x-noteheads, snare notes, hihat+snare chords, hidden rests in both voices
- Kick uses 8 eighth-note slots so off-beat kicks (e.g. Cha-tu, Punk) render correctly
- Beaming: consecutive pairs of non-rest voiceUp notes beamed per beat (positions 0+1, 2+3, 4+5, 6+7)

## Commit

a50d3fa
