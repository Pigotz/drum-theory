<script setup lang="ts">
import { ref, onMounted, nextTick } from 'vue'
import { onSlideEnter } from '@slidev/client'
import { Renderer, Stave, StaveNote, Voice, Formatter, Stem, Beam } from 'vexflow'

// Multi-bar grooves (16 steps) render as bars side-by-side on one row.
// Single-bar grooves (8 steps) behave exactly as before.
const props = withDefaults(defineProps<{
  groove?: { hh: number[]; sn: number[]; kk: number[] }
  width?: number
  height?: number
}>(), {
  groove: () => ({
    hh: [1, 1, 1, 1, 1, 1, 1, 1],
    sn: [0, 0, 1, 0, 0, 0, 1, 0],
    kk: [1, 0, 0, 0, 1, 0, 0, 0],
  }),
  width: 480,
  height: 150,
})

const container = ref<HTMLElement | null>(null)
const COLORS = { kick: '#ef4444', snare: '#3b82f6', hihat: '#eab308' }

function buildBar(barHh: number[], barSn: number[], barKk: number[]) {
  const upNotes: StaveNote[] = barHh.map((hhHit, i) => {
    const snHit = barSn[i]
    if (hhHit && snHit) {
      const note = new StaveNote({ keys: ['g/5/x', 'c/5'], duration: '8', stemDirection: Stem.UP })
      note.setKeyStyle(0, { fillStyle: COLORS.hihat, strokeStyle: '#e2e8f0' })
      note.setKeyStyle(1, { fillStyle: COLORS.snare, strokeStyle: '#e2e8f0' })
      note.setStemStyle({ fillStyle: '#e2e8f0', strokeStyle: '#e2e8f0' })
      return note
    } else if (hhHit) {
      const note = new StaveNote({ keys: ['g/5/x'], duration: '8', stemDirection: Stem.UP })
      note.setStyle({ fillStyle: COLORS.hihat, strokeStyle: COLORS.hihat })
      return note
    } else if (snHit) {
      const note = new StaveNote({ keys: ['c/5'], duration: '8', stemDirection: Stem.UP })
      note.setStyle({ fillStyle: COLORS.snare, strokeStyle: COLORS.snare })
      return note
    } else {
      const note = new StaveNote({ keys: ['b/4'], duration: '8r', stemDirection: Stem.UP })
      note.setStyle({ fillStyle: 'none', strokeStyle: 'none' })
      return note
    }
  })

  const downNotes: StaveNote[] = barKk.map(hit => {
    const note = new StaveNote({ keys: ['f/4'], duration: hit ? '8' : '8r', stemDirection: Stem.DOWN })
    note.setStyle(hit
      ? { fillStyle: COLORS.kick, strokeStyle: COLORS.kick }
      : { fillStyle: 'none', strokeStyle: 'none' })
    return note
  })

  return { upNotes, downNotes }
}

async function render() {
  await nextTick()
  if (!container.value) return
  container.value.innerHTML = ''

  try {
    const { hh, sn, kk } = props.groove
    const numBars = Math.max(1, Math.round(hh.length / 8))

    // All bars share the same single row — height is always props.height
    const renderer = new Renderer(container.value as HTMLDivElement, Renderer.Backends.SVG)
    renderer.resize(props.width, props.height)
    const ctx = renderer.getContext()
    ctx.setStrokeStyle('#e2e8f0')
    ctx.setFillStyle('#e2e8f0')

    // Distribute horizontal space: bar 0 carries clef+timesig overhead (~60px),
    // subsequent bars have a small left margin (~10px).
    const totalStaveWidth = props.width - 20
    const clefOverhead = 60
    const barMargin = 10
    const noteSpace = Math.floor((totalStaveWidth - clefOverhead - barMargin * (numBars - 1)) / numBars)

    let x = 10
    for (let bar = 0; bar < numBars; bar++) {
      const isFirst = bar === 0
      const overhead = isFirst ? clefOverhead : barMargin
      // Last bar absorbs any rounding remainder
      const barWidth = bar === numBars - 1
        ? (props.width - 10 - x)
        : noteSpace + overhead

      const start = bar * 8
      const barHh = hh.slice(start, start + 8)
      const barSn = sn.slice(start, start + 8)
      const barKk = kk.slice(start, start + 8)

      const stave = new Stave(x, 20, barWidth)
      if (isFirst) stave.addClef('percussion').addTimeSignature('4/4')
      stave.setContext(ctx).draw()

      const { upNotes, downNotes } = buildBar(barHh, barSn, barKk)

      const voice1 = new Voice({ numBeats: 4, beatValue: 4 }).addTickables(upNotes)
      const voice2 = new Voice({ numBeats: 4, beatValue: 4 }).addTickables(downNotes)
      new Formatter().joinVoices([voice1, voice2]).format([voice1, voice2], noteSpace)

      const beams: Beam[] = []
      for (let i = 0; i < 8; i += 2) {
        if ((barHh[i] === 1 || barSn[i] === 1) && (barHh[i + 1] === 1 || barSn[i + 1] === 1)) {
          const beam = new Beam([upNotes[i], upNotes[i + 1]])
          beam.setStyle({ fillStyle: COLORS.hihat, strokeStyle: COLORS.hihat })
          beams.push(beam)
        }
      }

      voice1.draw(ctx, stave)
      voice2.draw(ctx, stave)
      beams.forEach(b => b.setContext(ctx).draw())

      x += barWidth
    }
  } catch (err) {
    console.error('[GrooveDrumStaff] VexFlow render error:', err)
  }
}

onMounted(render)
onSlideEnter(render)
</script>

<template>
  <div ref="container" />
</template>
