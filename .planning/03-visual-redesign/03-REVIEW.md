---
phase: 03-visual-redesign
reviewed: 2026-04-19T00:00:00Z
depth: standard
files_reviewed: 4
files_reviewed_list:
  - style.css
  - slides.md
  - components/DrumStaff.vue
  - components/LoopPlayer.vue
findings:
  critical: 2
  warning: 4
  info: 3
  total: 9
status: issues_found
---

# Phase 03: Code Review Report

**Reviewed:** 2026-04-19
**Depth:** standard
**Files Reviewed:** 4
**Status:** issues_found

## Summary

Reviewed four source files: the global stylesheet, the Slidev presentation deck, the VexFlow drum notation component, and the audio loop player. Two critical issues were found — both asset path bugs that will cause visible failures on the GitHub Pages deployment. Four warnings cover unhandled promise rejection risk and state-sync issues in LoopPlayer, plus a latent beaming bug in DrumStaff. Three info items cover typing, accessibility, and a font-loading reliability concern.

---

## Critical Issues

### CR-01: Bare absolute paths for slide image and SVG assets will 404 on GitHub Pages

**File:** `slides.md:23-24, 39, 67, 95, 123, 151`

**Issue:** The `image:` frontmatter on the drum-sheet slide uses `/drum-sheet.png`. The five note-symbol `<img>` tags use `/svgs/whole-note.svg`, `/svgs/half-note.svg`, etc. All of these are bare absolute paths. CLAUDE.md explicitly requires `import.meta.env.BASE_URL` for runtime asset paths. With `base: /drum-theory/` set in the frontmatter, the dev server resolves these correctly, but the GitHub Pages build will serve the app under `/drum-theory/` and the browser will request `/drum-sheet.png` (no base prefix), producing 404s for the image slide and all five note slides during the actual talk.

**Fix:** For the frontmatter `image:` field, prefix with the base path or rely on a public-folder-relative path that Vite rewrites. In slides.md, Slidev accepts relative-to-public paths when you omit the leading slash only if Vite handles the rewrite — the safest fix is to change the frontmatter to:
```yaml
image: /drum-theory/drum-sheet.png
```
For the `<img>` tags inside slide content, switch to a dynamic binding that reads the base URL:
```html
<!-- Instead of: -->
<img src="/svgs/whole-note.svg" ... />

<!-- Use: -->
<img :src="$slidev.configs.base + 'svgs/whole-note.svg'" ... />
```
Or move the images to the Slidev public folder and rely on Vite's asset handling by using a relative path without a leading slash (e.g., `src="svgs/whole-note.svg"`), which Vite will rebase automatically.

---

### CR-02: `audio.value.play()` unhandled rejection causes silent state desync

**File:** `components/LoopPlayer.vue:13-16`

**Issue:** `audio.value.play()` returns a Promise. Modern browsers reject it when autoplay policy blocks playback (which is common on first interaction before any user gesture has been registered on the audio context). The rejection is unhandled, so (a) a console error fires, (b) the audio does not play, but (c) `playing.value` is set to `true` unconditionally on line 16. The UI shows "Stop" while silence plays. Pressing the button again calls `audio.value.pause()` on a track that never started, and toggles to `playing = false` — the player is now permanently out of sync until page reload. During a live presentation this will be visible to the audience.

**Fix:**
```typescript
const toggle = async () => {
  if (playing.value) {
    audio.value!.pause()
    playing.value = false
  } else {
    try {
      await audio.value!.play()
      playing.value = true
    } catch (err) {
      // Autoplay blocked — don't update state; next click will retry
      console.warn('[LoopPlayer] play() blocked:', err)
    }
  }
}
```

---

## Warnings

### WR-01: `audio.value` accessed without null guard in `toggle()`

**File:** `components/LoopPlayer.vue:12-13`

**Issue:** `audio` is declared as `ref(null)`. In `toggle()`, `audio.value.pause()` and `audio.value.play()` are called with no null check. While the `<audio>` element is always rendered in the template so `audio.value` will be set by mount time in normal use, the absence of a guard is fragile — if the component is ever used in an SSR context or in a test harness without a real DOM, this throws a TypeError at runtime. CLAUDE.md requires null-guarding refs (VexFlow section, but the principle applies).

**Fix:**
```typescript
const toggle = async () => {
  if (!audio.value) return
  // ... rest of logic
}
```

---

### WR-02: Beam color hard-coded to hihat yellow regardless of note instrument

**File:** `components/DrumStaff.vue:131`

**Issue:** All beams in `voiceUp` are styled with `COLORS.hihat` (`#eab308`). Currently the only voiceUp eighth-note patterns use hi-hat notes, so this works. But `DrumPattern` accepts arbitrary patterns: if a future pattern places snare-only eighth notes in `voiceUp`, the connecting beam will be drawn in hihat yellow rather than snare blue. This is a visual correctness bug for any pattern that deviates from the current groove structure.

**Fix:** Determine beam color from the actual instrument of the notes being beamed:
```typescript
for (let i = 0; i + 1 < eighthNotes.length; i += 2) {
  const beam = new Beam(eighthNotes.slice(i, i + 2))
  // Use instrument of first note in beam group
  const beatIndex = upNotes.indexOf(eighthNotes[i])
  const instrument = props.pattern.voiceUp[beatIndex]?.instrument ?? 'hihat'
  beam.setStyle({ fillStyle: COLORS[instrument], strokeStyle: COLORS[instrument] })
  beams.push(beam)
}
```

---

### WR-03: Beam grouping loses positional context after filtering

**File:** `components/DrumStaff.vue:128-130`

**Issue:** `upNotes.filter((_, i) => props.pattern.voiceUp[i].duration === '8')` produces a filtered array where positional index no longer maps to beat position. If a pattern mixes durations (e.g., `['8', 'q', '8', '8']`), the filtered array is `[note0, note2, note3]`. The loop beams `[note0, note2]` and leaves `note3` unbeamed — but `note2` and `note3` are adjacent in the voice and should be beamed together. The logic beams the wrong pair.

**Fix:** Walk `upNotes` sequentially to collect runs of consecutive eighth notes instead of filtering:
```typescript
const beams: Beam[] = []
let i = 0
while (i < upNotes.length) {
  if (props.pattern.voiceUp[i].duration === '8') {
    const run: StaveNote[] = []
    while (i < upNotes.length && props.pattern.voiceUp[i].duration === '8') {
      run.push(upNotes[i++])
    }
    for (let j = 0; j + 1 < run.length; j += 2) {
      const beam = new Beam(run.slice(j, j + 2))
      beam.setStyle({ fillStyle: COLORS.hihat, strokeStyle: COLORS.hihat })
      beams.push(beam)
    }
  } else {
    i++
  }
}
```

---

### WR-04: CSS selector for right-column background may match unintended children

**File:** `style.css:23-25`

**Issue:** `.slidev-layout.two-cols > *:last-child` targets the last direct child of the two-cols layout container. This happens to be the right column today, but the selector is structural, not semantic. If Slidev's internal DOM adds a wrapper element or if a slide adds more children to the layout container, the background color shifts to a different element silently. This is fragile for a visual redesign phase where layout DOM may change.

**Fix:** If Slidev exposes a class for the right slot (check Slidev's generated HTML), use it directly:
```css
.slidev-layout.two-cols .col-right {
  background: #1a1d27;
}
```
If no stable class exists, confirm the selector still works after the visual redesign implementation and leave a comment explaining the intent:
```css
/* Right column of two-cols layout — relies on being the last direct child */
.slidev-layout.two-cols > *:last-child {
  background: #1a1d27;
}
```

---

## Info

### IN-01: Static `aria-label` on DrumStaff will be inaccurate for any non-groove1 pattern

**File:** `components/DrumStaff.vue:153-155`

**Issue:** The `aria-label` on the container `<div>` is a hard-coded string: `"Drum notation: basic rock groove with kick on beats 1 and 3, snare on 2 and 4, hi-hat every eighth note"`. This is accurate for `groove1` only. If DrumStaff is ever used with a different pattern, screen readers will announce incorrect information. The component accepts an arbitrary `pattern` prop, so the label should be dynamic.

**Fix:** Accept an optional `label` prop and fall back to a generic description:
```typescript
const props = withDefaults(defineProps<{
  pattern: DrumPattern
  width?: number
  height?: number
  label?: string
}>(), { width: 500, height: 180, label: 'Drum notation pattern' })
```
```html
<div ref="container" role="img" :aria-label="label" />
```

---

### IN-02: `LoopPlayer.vue` lacks TypeScript and has untyped `audio` ref

**File:** `components/LoopPlayer.vue:1, 5`

**Issue:** `<script setup>` has no `lang="ts"` while DrumStaff.vue uses TypeScript. `audio` is declared as `ref(null)` with no type annotation, making `audio.value` typed as `null` — TypeScript would flag the `.pause()` and `.play()` calls if strict mode were enabled. This is also why the null-access bugs in WR-01/CR-02 are not caught at build time.

**Fix:**
```typescript
<script setup lang="ts">
import { ref } from 'vue'
import { onSlideLeave } from '@slidev/client'

const audio = ref<HTMLAudioElement | null>(null)
```

---

### IN-03: Google Fonts loaded via external CDN — network dependency during live talk

**File:** `style.css:1`

**Issue:** `@import url('https://fonts.googleapis.com/css2?family=Space+Grotesk...')` requires internet access at presentation time. If the venue WiFi is unavailable or blocked, Space Grotesk won't load and the presentation falls back to the browser's default `sans-serif`. This is not a crash, but it may noticeably affect the visual redesign aesthetics.

**Fix:** Download the Space Grotesk font files (woff2) and self-host them in the `public/` directory, then use a local `@font-face` declaration. This removes the network dependency entirely.

---

_Reviewed: 2026-04-19_
_Reviewer: Claude (gsd-code-reviewer)_
_Depth: standard_
