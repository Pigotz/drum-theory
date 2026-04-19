# Drum Theory Presentation — Full Brief

## Context

A "PowerPoint night" presentation for a non-technical, music-theory-naive audience (close friends). The goal is to **entertain while teaching something real**. The presenter is a drummer. Total duration: **maximum 12 minutes**.

---

## Presentation Title

**"You Can't Read This"**
*A drummer's guide to the language nobody asked for*

---

## Structure & Timing

| Act | Content | Time |
|-----|---------|------|
| Act 1 | Note durations | ~3 min |
| Act 2 | Drum notation map | ~2 min |
| Act 3 | Transition to practical *(one line, not a full act)* | ~30 sec |
| Act 4 | Human Drum session | ~5–6 min |

**Time risk:** The clapping/demo moments in Act 1 are the biggest risk. If the audience engages and it breathes, Act 1 alone could eat 5 minutes. Be ready to skip the sixteenth note example if running long — it's the easiest to cut.

---

## Content — Act 1: Note Durations (~3 min)

### Hook (Slide 2)
Show a real drum sheet (image, full screen). No explanation. Caption: *"By the end of this, you'll be able to read this."*

### Note Duration Ladder (Slides 3–7, one slide per note)

Use a **pizza analogy**:

| Note | Duration | Pizza Analogy |
|------|----------|---------------|
| Whole note | 4 beats | The whole pizza — you wait forever |
| Half note | 2 beats | Half a pizza |
| Quarter note | 1 beat | A slice — this is the normal pulse, what you tap your foot to |
| Eighth note | ½ beat | Half a slice — two fit in one beat ("tss-tss") |
| Sixteenth note | ¼ beat | A tiny bite — four fit in one beat, things get spicy |

For each note: show the symbol first, then reveal the analogy. Have the audience clap along. Make it physical from the start.

### Key insight to land (Slide 8 — statement slide)
> *"The beat doesn't speed up. You just fit more sounds inside the same time."*

This is the mic-drop moment. Let it breathe. One sentence, huge text, center of screen.

---

## Content — Act 2: Drum Notation (~2 min)

### Core points (Slides 9–10)

- Unlike piano/guitar, drum notes don't represent **pitch** — they represent **which drum to hit**
- Position on the staff = the instrument
- Standard layout:
  - **Hi-hat** → top of staff (x noteheads, above the lines) — color: **yellow**
  - **Snare** → middle (3rd space) — color: **blue**
  - **Kick** → bottom (below the staff) — color: **red**
- The "why": drums are a *coordination* instrument. The sheet isn't a melody, it's a **map of what each limb is doing simultaneously**

### Optional comedy beat
Show a gnarly-looking fill and say: *"This is what I do so you don't have to think about the beat anymore."*

---

## Content — Act 3: Transition (one line)

> *"Rhythm is the skeleton of music. Now let's prove it."*

No separate slide needed — fold into the end of Act 2.

---

## Content — Act 4: Human Drum Session (~5–6 min)

### Volunteer roles (reveal one at a time with click animations)

| Role | Title | Sound | When |
|------|-------|-------|------|
| Kick | Chief Kick Officer | **"BOOM"** | Beats 1 and 3 |
| Snare | Snare Department Head | **"PAH"** | Beats 2 and 4 |
| Hi-hat | Hi-Hat Technician | **"tss"** | Every eighth note |

### Grooves to run (escalating complexity)

**Groove 1 — Basic rock:**
```
Beat:   1    +    2    +    3    +    4    +
HH:     x    x    x    x    x    x    x    x
SN:          x              x
KK:     x              x
```

**Groove 2 — Variation (add double kick):**
Same as above but add kick on the "and" of 4. One change, but it feels like a different genre. Point that out explicitly.

**Groove 3 — Chaos round (optional, depends on energy):**
Freestyle-conduct like an orchestra conductor. Point at people to start/stop/speed up. Pure comedy. Usually ends in collapse. Perfect ending.

### Backing track
Play a **drumless backing track** in loop during the Human Drum session. Ideal tempo: ~90 BPM. A bassline-only loop works best. Source options: YouTube "drumless" versions, Splice, GarageBand export, EZdrummer.

---

## Slide Plan (Slidev)

### Global config (`slides.md` frontmatter)

```yaml
---
theme: seriph
title: "You Can't Read This"
highlighter: shiki
drawings:
  persist: false
transition: slide-left
base: /drum-theory/   # required if hosting on GitHub Pages under a subdirectory
---
```

### Slide breakdown

| # | Layout | Content | Slidev feature |
|---|--------|---------|----------------|
| 1 | `cover` | Title + subline | — |
| 2 | `image` | Full-screen drum sheet screenshot | Hook |
| 3 | `two-cols` | Whole note symbol + pizza analogy | `v-click` reveal |
| 4 | `two-cols` | Half note symbol + pizza analogy | `v-click` reveal |
| 5 | `two-cols` | Quarter note symbol + pizza analogy | `v-click` reveal |
| 6 | `two-cols` | Eighth note symbol + pizza analogy | `v-click` reveal |
| 7 | `two-cols` | Sixteenth note symbol + pizza analogy | `v-click` reveal |
| 8 | `statement` | *"The beat doesn't speed up..."* | Big text, no extras |
| 9 | `image-right` | Staff diagram (kick/snare/hi-hat) + bullet explanation | Color-coded diagram |
| 10 | `quote` or `statement` | *"This isn't a melody. It's a map of what each limb does simultaneously."* | — |
| 11 | `default` | Volunteer roles | `v-clicks` list |
| 12 | `two-cols` | Groove #1 grid + volunteer instructions | HTML color grid |
| 13 | `end` | *"Thanks for being my drum kit."* | Optional QR code |

### Presenter notes
Add `<!-- note -->` blocks at the bottom of every slide. Displayed in Slidev's presenter view (laptop only). Use for spoken cues and timing reminders. Example:
```
<!-- Clap the quarter note 4 times. Ask them to join. Don't move on until they're clapping. ~30 seconds. -->
```

---

## Optional Features

### 1. VexFlow drum notation (replaces screenshots)
**Effort: Medium.** Budget one evening for the component.

- Install: `npm install vexflow`
- Create `components/DrumStaff.vue` — renders notation via VexFlow on Canvas/SVG
- Accepts note data as props
- Percussion-specific config needed: x-noteheads, percussion clef
- Drop `<DrumStaff />` directly in slide Markdown

### 2. Playable notation (click-to-play audio on each notation slide)
**Effort: Medium-high.** Build on top of VexFlow after visuals are solid.

- Use **Tone.js** for audio scheduling
- Source free drum samples (open-license WAV) from Freesound.org
- Place samples in `public/samples/`
- Extend `DrumStaff.vue` into `PlayableDrumStaff.vue` with play button
- Add a visual beat cursor that moves across the staff in sync with audio
- Pattern data structure drives both VexFlow rendering and Tone.js scheduling

### 3. Drumless backing track player (for Human Drum slide)
**Effort: Low.** ~1 hour including finding the track.

- Place MP3/WAV in `public/`
- Create `components/LoopPlayer.vue`:

```vue
<script setup>
import { ref } from 'vue'
const audio = ref(null)
const playing = ref(false)
const toggle = () => {
  if (playing.value) { audio.value.pause() }
  else { audio.value.play() }
  playing.value = !playing.value
}
</script>

<template>
  <audio ref="audio" src="/backing.mp3" loop />
  <button @click="toggle">{{ playing ? '⏹ Stop' : '▶ Play' }}</button>
</template>
```

- Place `<LoopPlayer src="/backing.mp3" />` on the Human Drum slide

### Recommended build order
1. Backing track player (quick win, no risk)
2. VexFlow static notation (visual foundation)
3. Playable notation (audio layer on top of VexFlow)

If time is tight: skip playable notation. Static VexFlow staves are already a big upgrade over screenshots, and the live Human Drum session does the "playable" job anyway.

---

## GitHub Pages Hosting

Slidev has first-class support. Full flow:

**1. Build:**
```bash
npm run build
# outputs to dist/
```

**2. Deploy options:**

*Option A — GitHub Actions (recommended):* Add `.github/workflows/deploy.yml` using Slidev's official workflow template. Auto-builds and pushes to `gh-pages` branch on every push to `main`.

*Option B — Manual:*
```bash
npm install -D gh-pages
# add to package.json scripts:
# "deploy": "gh-pages -d dist"
npm run deploy
```

**3. Critical gotcha — base path:**
If hosted at `yourname.github.io/drum-theory`, set in frontmatter:
```yaml
base: /drum-theory/
```
Without this, assets will 404.

**4. Enable Pages in repo settings:** Settings → Pages → source: `gh-pages` branch.

---

## Design Guidelines

- **Theme:** `seriph` (dark background — notation pops, drummer aesthetic)
- **Color coding (consistent throughout):** kick = red, snare = blue, hi-hat = yellow
- **One concept per slide** — no overloading
- **Minimal text** — let the notation and analogies be the visual
- **`v-click` reveals** — show symbol first, let it land, then reveal the analogy
- Short video clips or GIFs of the presenter playing each example beat are more effective than any amount of text explanation