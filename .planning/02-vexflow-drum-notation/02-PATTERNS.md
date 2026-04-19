# Phase 2: VexFlow Drum Notation - Pattern Map

**Mapped:** 2026-04-19
**Files analyzed:** 2 (1 new component, 1 modified slide file)
**Analogs found:** 2 / 2

---

## File Classification

| New/Modified File | Role | Data Flow | Closest Analog | Match Quality |
|-------------------|------|-----------|----------------|---------------|
| `components/DrumStaff.vue` | component | transform (data → SVG render) | `components/LoopPlayer.vue` | role-match |
| `slides.md` (slide 9 section, lines 189–215) | config | request-response (frontmatter → layout) | `slides.md` lines 34–60 (two-cols with SVG) | exact |

---

## Pattern Assignments

### `components/DrumStaff.vue` (component, transform)

**Analog:** `components/LoopPlayer.vue`

**Imports pattern** (LoopPlayer.vue lines 1–3):
```vue
<script setup>
import { ref } from 'vue'
```

**Extension for DrumStaff.vue** — add VexFlow and Slidev lifecycle imports on top of this pattern:
```typescript
import { ref, onMounted } from 'vue'
import { onSlideEnter } from '@slidev/client'
import { Renderer, Stave, StaveNote, Voice, Formatter, Stem, Beam } from 'vexflow'
```

**Ref + null guard pattern** (LoopPlayer.vue lines 4–5, CLAUDE.md):
```vue
<script setup>
const audio = ref(null)        // LoopPlayer pattern: typed ref for DOM element
```

DrumStaff.vue copies this exactly:
```typescript
const container = ref<HTMLElement | null>(null)

onMounted(() => {
  if (!container.value) return   // mandatory null guard — CLAUDE.md locked constraint
  // ... VexFlow init
})
```

**Template ref pattern** (LoopPlayer.vue lines 19–26):
```vue
<template>
  <audio
    ref="audio"
    :src="src"
    loop
  />
```

DrumStaff.vue analog — container div as mount point:
```vue
<template>
  <div
    ref="container"
    role="img"
    aria-label="Drum notation: basic rock groove with kick on beats 1 and 3, snare on 2 and 4, hi-hat every eighth note"
  />
</template>
```

**Core render pattern** — VexFlow SVG initialization (RESEARCH.md Pattern 1):
```typescript
function render() {
  if (!container.value) return
  container.value.innerHTML = ''   // clear before re-render (Pitfall 3)

  try {
    // 1. Renderer — SVG backend only (CLAUDE.md locked)
    const renderer = new Renderer(container.value, Renderer.Backends.SVG)
    renderer.resize(props.width, props.height)
    const ctx = renderer.getContext()

    // 2. Global white context for dark seriph background (D-09)
    ctx.setFillStyle('white')
    ctx.setStrokeStyle('white')

    // 3. Percussion stave
    const staveX = 10, staveY = 30
    const staveWidth = props.width - staveX - 10
    const stave = new Stave(staveX, staveY, staveWidth)
    stave.addClef('percussion').addTimeSignature('4/4')
    stave.setContext(ctx).draw()

    // 4. Build notes, color BEFORE draw (Pitfall 4), then format + draw
    // ... (see RESEARCH.md Patterns 2–5)
  } catch (err) {
    console.error('[DrumStaff] VexFlow render error:', err)  // D-13 silent fail
  }
}

onMounted(render)
onSlideEnter(render)   // re-render on slide re-entry (CLAUDE.md, Pitfall 3)
```

**Props pattern** — copy `withDefaults` + `defineProps` (no analog in LoopPlayer; use RESEARCH.md):
```typescript
const props = withDefaults(defineProps<{
  pattern: DrumPattern
  width?: number
  height?: number
}>(), { width: 500, height: 180 })
```

**Color constants** — inline COLORS object (mirrors groove grid inline colors in slides.md lines 284–307):
```typescript
const COLORS = { kick: '#ef4444', snare: '#3b82f6', hihat: '#eab308' }
```

**Error handling pattern** (D-13):
```typescript
try {
  // ... VexFlow initialization
} catch (err) {
  console.error('[DrumStaff] VexFlow render error:', err)
  // Leave container empty — presentation continues without crashing
}
```

**No `<style>` block** — LoopPlayer.vue uses inline styles only; DrumStaff.vue follows the same convention. No external CSS framework. No `overflow: hidden` on the container (RESEARCH.md anti-patterns).

---

### `slides.md` — slide 9 section migration (lines 189–215)

**Analog:** `slides.md` lines 34–60 — `two-cols` layout with left content + `::right::` slot + SVG image in right column.

**Frontmatter migration pattern** (slides.md line 34–36):
```markdown
---
layout: two-cols
---
```

Replace the current slide 9 frontmatter:
```markdown
--- before ---
---
layout: image-right
image: /drum-sheet.png
---

--- after ---
---
layout: two-cols
---
```

**Two-cols slot pattern** (slides.md lines 43–55):
```markdown
# Left column content goes here (no slot marker needed)

::right::

<v-click>   <!-- optional v-click wrapper — NOT used for DrumStaff (D on slide 9: shown immediately) -->
...right column content...
</v-click>
```

DrumStaff.vue variant for slide 9 right column:
```markdown
::right::

<script setup>
const groove1 = {
  beats: 4, beatValue: 4,
  voiceUp: [
    { instrument: 'hihat', position: 'a/5', duration: '8', noteType: 'x' },
    { instrument: 'hihat', position: 'a/5', duration: '8', noteType: 'x' },
    { instrument: 'hihat', position: 'a/5', duration: '8', noteType: 'x' },  // beat 2: add snare chord in implementation
    { instrument: 'hihat', position: 'a/5', duration: '8', noteType: 'x' },
    { instrument: 'hihat', position: 'a/5', duration: '8', noteType: 'x' },
    { instrument: 'hihat', position: 'a/5', duration: '8', noteType: 'x' },
    { instrument: 'hihat', position: 'a/5', duration: '8', noteType: 'x' },  // beat 4: add snare chord in implementation
    { instrument: 'hihat', position: 'a/5', duration: '8', noteType: 'x' },
  ],
  voiceDown: [
    { instrument: 'kick', position: 'e/4', duration: 'q' },
    { instrument: 'kick', position: 'e/4', duration: 'q', rest: true },
    { instrument: 'kick', position: 'e/4', duration: 'q' },
    { instrument: 'kick', position: 'e/4', duration: 'q', rest: true },
  ]
}
</script>

<DrumStaff :pattern="groove1" />
```

**Left column content** — preserved unchanged from lines 194–208 (bullets with inline color styles, `list-style: none`).

**Speaker note update** (lines 210–215):
```markdown
<!--
Spoken: ...existing spoken lines unchanged...
Timing: ~90 seconds. Point at colors as you name them.
Right panel: live VexFlow percussion stave — point at colors as you name them.
-->
```

---

## Shared Patterns

### Null Guard (onMounted)
**Source:** `components/LoopPlayer.vue` line 4 + CLAUDE.md
**Apply to:** `DrumStaff.vue` — all DOM ref access
```typescript
const container = ref<HTMLElement | null>(null)
onMounted(() => {
  if (!container.value) return
  // ...
})
```

### Inline Color Constants (kick/snare/hihat)
**Source:** `slides.md` lines 284–307 (groove grid table inline styles)
**Apply to:** `DrumStaff.vue` COLORS object — same three hex values used for VexFlow `setStyle` calls
```typescript
// kick = #ef4444, snare = #3b82f6, hihat = #eab308
// These exact values appear in slides.md groove grid cells and are locked in CLAUDE.md
```

### Two-Cols Frontmatter Pattern
**Source:** `slides.md` line 35 (and lines 63, 91, 119, 147, 264 — same pattern repeated)
**Apply to:** Slide 9 frontmatter migration
```markdown
---
layout: two-cols
---
```

### Inline Styles (no CSS framework)
**Source:** `components/LoopPlayer.vue` lines 28–38 — all styles as inline `style=""` attributes
**Apply to:** `DrumStaff.vue` — if any wrapper div needs styling, use inline styles. No `<style>` scoped block, no Tailwind.
```vue
style="background: #6366f1; color: white; font-weight: 700; ..."
```

### No `onMounted`/`onUnmounted` for Slide Lifecycle
**Source:** CLAUDE.md (locked)
**Apply to:** `DrumStaff.vue` — use `onSlideEnter` from `@slidev/client` for re-entry; do NOT rely on `onMounted` alone
```typescript
import { onSlideEnter } from '@slidev/client'
onSlideEnter(render)
```

---

## No Analog Found

| File | Role | Data Flow | Reason |
|------|------|-----------|--------|
| (none) | — | — | Both files have clear analogs in the codebase |

---

## Key Anti-Patterns (from RESEARCH.md — planner must include these as warnings)

| Anti-Pattern | Where It Bites | Correct Approach |
|-------------|----------------|------------------|
| `noteType: 'x'` in StaveNote constructor | Silent standard notehead | Use `type: 'x'` or `'a/5/x'` per-key syntax |
| `setStyle()` called after `voice.draw()` | Colors don't apply | ALL `setStyle`/`setKeyStyle` calls BEFORE first `voice.draw()` |
| `container.innerHTML` not cleared on re-entry | Double SVG stave | `container.value.innerHTML = ''` at top of render function |
| `overflow: hidden` on container div | Clef/flags clipped | No `overflow: hidden` |
| Canvas backend | CLAUDE.md violation | `Renderer.Backends.SVG` always |

---

## Metadata

**Analog search scope:** `components/`, `slides.md`
**Files scanned:** 2 source files + 4 planning docs
**Pattern extraction date:** 2026-04-19
