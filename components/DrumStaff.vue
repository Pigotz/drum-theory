<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { onSlideEnter } from '@slidev/client'
import { Renderer, Stave, StaveNote, Voice, Formatter, Stem, Beam } from 'vexflow'

interface DrumBeatChordMate {
  instrument: 'snare' | 'hihat' | 'kick'
  position: string
}

interface DrumBeat {
  instrument: 'kick' | 'snare' | 'hihat'
  position: string
  duration: string
  noteType?: 'x'
  rest?: boolean
  chordMate?: DrumBeatChordMate  // D-07: second instrument stacked on same stem
}

interface DrumPattern {
  beats?: number
  beatValue?: number
  voiceUp: DrumBeat[]
  voiceDown: DrumBeat[]
}

const props = withDefaults(defineProps<{
  pattern: DrumPattern
  width?: number
  height?: number
}>(), { width: 500, height: 180 })

const container = ref<HTMLElement | null>(null)

const COLORS = { kick: '#ef4444', snare: '#3b82f6', hihat: '#eab308' }

function render() {
  if (!container.value) return           // mandatory null guard (CLAUDE.md)
  container.value.innerHTML = ''          // clear before re-render (prevents double stave on re-entry)

  try {
    // 1. SVG renderer (SVG backend only — CLAUDE.md locked)
    const renderer = new Renderer(container.value, Renderer.Backends.SVG)
    renderer.resize(props.width, props.height)
    const ctx = renderer.getContext()

    // 3. Percussion stave
    const staveX = 10
    const staveY = 30
    const staveWidth = props.width - staveX - 10
    const stave = new Stave(staveX, staveY, staveWidth)
    stave.addClef('percussion').addTimeSignature('4/4')
    ctx.setStrokeStyle('#e2e8f0')
    ctx.setFillStyle('#e2e8f0')
    stave.setContext(ctx).draw()

    // 4. Build voiceUp notes (hi-hat and snare, stems up)
    //    D-07: beats 2 and 4 are hi-hat+snare chords — signalled by beat.chordMate
    //    For a chord beat: keys = ['a/5/x', 'c/5']
    //      key[0] = hi-hat with x-notehead (per-key /x syntax)
    //      key[1] = snare with standard oval notehead
    //    For a plain hi-hat beat: keys = ['a/5/x']
    //    CRITICAL: do NOT pass noteType to StaveNote constructor — use per-key 'position/x' syntax
    const upNotes: StaveNote[] = props.pattern.voiceUp.map((beat) => {
      const primaryKey = beat.noteType === 'x' ? `${beat.position}/x` : beat.position
      const keys = beat.chordMate
        ? [primaryKey, beat.chordMate.position]
        : [primaryKey]
      return new StaveNote({
        keys,
        duration: beat.duration,
        stem_direction: Stem.UP,
      })
    })

    // 5. Build voiceDown notes (kick, stems down)
    const downNotes: StaveNote[] = props.pattern.voiceDown.map((beat) => {
      const duration = beat.rest ? `${beat.duration}r` : beat.duration
      return new StaveNote({
        keys: [beat.position],
        duration,
        stem_direction: Stem.DOWN,
      })
    })

    // 6. Color ALL notes BEFORE draw (D-11; Pitfall 4: setStyle after draw has no effect)
    //    For chord notes: use setKeyStyle per key index to color noteheads independently
    //    For single-instrument notes: use setStyle on the whole note
    props.pattern.voiceUp.forEach((beat, i) => {
      if (beat.chordMate) {
        // Chord beat: color each notehead independently (RESEARCH.md Pattern 4)
        upNotes[i].setKeyStyle(0, { fillStyle: COLORS[beat.instrument], strokeStyle: COLORS[beat.instrument] })
        upNotes[i].setKeyStyle(1, { fillStyle: COLORS[beat.chordMate.instrument], strokeStyle: COLORS[beat.chordMate.instrument] })
      } else {
        // Single-instrument beat: color the whole note
        upNotes[i].setStyle({
          fillStyle: COLORS[beat.instrument],
          strokeStyle: COLORS[beat.instrument],
        })
      }
    })
    props.pattern.voiceDown.forEach((beat, i) => {
      if (beat.rest) {
        downNotes[i].setStyle({ fillStyle: 'none', strokeStyle: 'none' })
      } else {
        downNotes[i].setStyle({
          fillStyle: COLORS[beat.instrument],
          strokeStyle: COLORS[beat.instrument],
        })
      }
    })

    // 7. Voices
    const numBeats = props.pattern.beats ?? 4
    const beatValue = props.pattern.beatValue ?? 4
    const voice1 = new Voice({ num_beats: numBeats, beat_value: beatValue })
      .addTickables(upNotes)
    const voice2 = new Voice({ num_beats: numBeats, beat_value: beatValue })
      .addTickables(downNotes)

    // 8. Format two voices together (staveWidth - 60 accounts for clef + time sig left margin)
    new Formatter()
      .joinVoices([voice1, voice2])
      .format([voice1, voice2], staveWidth - 60)

    // 9. Beam eighth notes in voiceUp — group consecutive eighths into beams of 2
    const beams: Beam[] = []
    const eighthNotes = upNotes.filter((_, i) => props.pattern.voiceUp[i].duration === '8')
    for (let i = 0; i + 1 < eighthNotes.length; i += 2) {
      const beam = new Beam(eighthNotes.slice(i, i + 2))
      beam.setStyle({ fillStyle: COLORS.hihat, strokeStyle: COLORS.hihat })
      beams.push(beam)
    }

    // 10. Draw voices then beams (beams must draw after voices)
    voice1.draw(ctx, stave)
    voice2.draw(ctx, stave)
    beams.forEach((b) => b.setContext(ctx).draw())

  } catch (err) {
    // D-13: silent fail — presentation keeps running with empty panel
    console.error('[DrumStaff] VexFlow render error:', err)
  }
}

onMounted(render)
onSlideEnter(render)   // re-render on slide re-entry (CLAUDE.md; Pitfall 3)
</script>

<template>
  <div
    ref="container"
    role="img"
    aria-label="Drum notation: basic rock groove with kick on beats 1 and 3, snare on 2 and 4, hi-hat every eighth note"
  />
</template>
