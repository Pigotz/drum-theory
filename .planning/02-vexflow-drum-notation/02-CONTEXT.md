# Phase 2: VexFlow Drum Notation - Context

**Gathered:** 2026-04-19
**Status:** Ready for planning

<domain>
## Phase Boundary

DrumStaff.vue renders a VexFlow SVG percussion stave in place of the color-grid placeholder on slide 9. Delivers static, color-coded notation (percussion clef, x-noteheads for hi-hat, standard noteheads for snare/kick, two-voice layout). PlayableDrumStaff.vue (Tone.js + beat cursor) is strictly out of scope for Phase 2.

</domain>

<decisions>
## Implementation Decisions

### Spike Strategy
- **D-01:** Plan includes a dedicated throwaway spike task first. The spike empirically validates the VexFlow v5 percussion API: `addClef('percussion')` call signature, x-notehead syntax, staff position strings (`a/5`, `c/5`, `e/4`), and `note.getAbsoluteX()` accessibility after `vf.draw()`. DrumStaff.vue is written only after spike results confirm the API.
- **D-02:** The spike also includes a VexFlow install smoke-test: `npm install vexflow@5.0.0`, confirm Vite can import it (ESM entry point). If Vite chokes on CJS, add `optimizeDeps.include` in Slidev's Vite config.

### Scope
- **D-03:** Slide 9 only. NOTE-01 "consistently applied throughout" means all slides use the same kick/snare/hihat color constants — not that DrumStaff appears on multiple slides.
- **D-04:** Slide 2 (`image: /drum-sheet.png`) is intentionally an overwhelming real drum sheet. Phase 2 does not touch slide 2 — user supplies the image separately.
- **D-05:** PlayableDrumStaff.vue (Tone.js + beat cursor) is strictly deferred to v2. Phase 2 ships static notation only, regardless of how fast implementation goes.

### VexFlow API Style
- **D-06:** Use the low-level VexFlow API: `Renderer`, `Stave`, `Voice`, `Formatter`, `StaveNote` directly. Gives full control over percussion-specific setup and per-note style overrides. Factory API is not used.

### Voice / Notehead Layout
- **D-07:** Hi-hat and snare share voiceUp. On beats 2 & 4 where they coincide, implement as VexFlow **chord notation** (stacked noteheads on one stem). The spike will confirm whether `setStyle` can color individual noteheads within a chord or applies to the whole chord — implementation adjusts accordingly.
- **D-08:** Kick lives in voiceDown with stems pointing down. Two-voice layout: Voice 1 (up) = hi-hat + snare chords, Voice 2 (down) = kick.

### Dark Background / Color Strategy
- **D-09:** After creating the VexFlow renderer, call `context.setFillStyle('white')` and `context.setStrokeStyle('white')` globally before any drawing. This makes stave lines, percussion clef, and barlines white on the dark seriph background. Per-note `setStyle()` then overrides kick/snare/hihat to their instrument colors on top.
- **D-10:** Stave lines, clef, and barlines rely on the global context override — no per-element color overrides needed for structural elements.
- **D-11:** Per-note coloring: `note.setStyle({ fillStyle: '<color>', strokeStyle: '<color>' })` called before `vf.draw()`. Colors: kick = `#ef4444`, snare = `#3b82f6`, hihat = `#eab308`.

### Groove Data Location
- **D-12:** The `groove1` DrumPattern constant is defined inline in the slide's `<script setup>` block within slides.md. No separate patterns.ts file. Self-contained and easy to edit per slide.

### Error Handling
- **D-13:** DrumStaff.vue wraps VexFlow initialization in try/catch. On error: `console.error` + leave container empty (silent fail). Presentation keeps running with an empty panel rather than crashing.

### Slide 9 Copy Update
- **D-14:** Left-column bullet list (hi-hat/snare/kick color bullets) is **unchanged** from Phase 1. Only the speaker note is updated: remove the "placeholder image" line, replace with "Right panel: live VexFlow percussion stave — point at colors as you name them."

### Slide 9 Layout Migration
- **D-15:** Migrate slide 9 from `layout: image-right` (which cannot mount Vue components in the image slot) to `layout: two-cols`. Left column keeps the bullet list; right column receives `<DrumStaff :pattern="groove1" />`.

### Fallback Plan
- **D-16:** If VexFlow percussion proves too complex or broken in v5, fallback is a hand-crafted SVG that visually mimics the percussion stave — colored noteheads on staff lines, no VexFlow dependency. The spike determines whether fallback is needed.

### Claude's Discretion
- Exact VexFlow `Formatter` / `Voice` beam configuration for the two-voice layout
- Whether `optimizeDeps.include` is needed in Slidev's Vite config (determined by install smoke-test in spike)
- Exact staff position strings (`a/5`, `c/5`, `e/4`) — confirm in spike, override from UI-SPEC estimates if VexFlow renders differently
- Chord color granularity (per-notehead vs. per-chord `setStyle`) — determined by spike findings

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Phase 2 Design Contract
- `.planning/02-vexflow-drum-notation/02-UI-SPEC.md` — **MUST READ** — locks DrumPattern interface, DrumBeat type, component props (pattern, width, height), VexFlow coloring contract, container sizing (500×180px defaults), Groove 1 pattern specification, slide 9 layout migration details, accessibility contract

### Stack Constraints
- `CLAUDE.md` — VexFlow SVG-only constraint, `onMounted()` guard pattern, `onSlideEnter`/`onSlideLeave` lifecycle rules, color constants (kick=`#ef4444`, snare=`#3b82f6`, hihat=`#eab308`), base path pattern

### Phase Requirements
- `.planning/REQUIREMENTS.md` — NOTE-01, NOTE-02, NOTE-03 acceptance criteria (three requirements this phase must satisfy)
- `.planning/ROADMAP.md` Phase 2 section — success criteria, open questions list, spike requirement rationale

### Phase 1 Prior Art
- `.planning/01-slide-skeleton-backing-track-deploy/01-CONTEXT.md` — prior decisions: color coding constants established, slot layout patterns, component import conventions
- `.planning/01-slide-skeleton-backing-track-deploy/01-UI-SPEC.md` — inherited design system (spacing, typography, color palette) that Phase 2 extends

### Presentation Content
- `slides.md` lines 189–215 — slide 9 current implementation (image-right layout to migrate, bullet list to preserve, speaker note to update)

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `components/LoopPlayer.vue` — only existing Vue component; establishes the pattern: `<script setup>`, `ref()`, `onMounted()` guard, no external CSS framework. DrumStaff.vue should follow the same conventions.
- `public/` — static assets directory (backing.mp3, drum-sheet.png, svgs/). DrumStaff.vue is a Vue component, not a static asset.
- `svgs/` directory — hand-crafted SVG files from Phase 1. If VexFlow fallback is needed, add the SVG staff here.

### Established Patterns
- Component imports: Slidev auto-imports from `components/` — no explicit import statement needed in slides.md
- Asset paths: `import.meta.env.BASE_URL + 'filename'` for public/ assets. VexFlow mounts to a DOM ref, not a URL.
- Lifecycle: `onMounted()` + null guard is the established pattern from CLAUDE.md. For slide re-entry, add `onSlideEnter` from `@slidev/client`.
- Inline script in slides: use `<script setup>` fenced block in slides.md for per-slide constants (groove1 pattern).

### Integration Points
- Slide 9 in slides.md: replace `layout: image-right` + `image: /drum-sheet.png` with `layout: two-cols` + `<DrumStaff :pattern="groove1" />` in the right column
- VexFlow mounts into a `<div ref="container">` — the container ref is passed to `new Vex.Flow.Renderer(container.value, Vex.Flow.Renderer.Backends.SVG)`
- No Tone.js, no audio — Phase 2 is display-only

</code_context>

<specifics>
## Specific Ideas

- The spike is the critical path item — it validates or invalidates the UI-SPEC's staff positions and API call signatures before any DrumStaff.vue code is committed
- Chord color granularity (can `setStyle` color individual noteheads within a chord?) is a known open question; if it can't, a three-voice approach may still be needed despite the preference for chords
- Fallback SVG staff should reuse the existing svgs/ directory pattern from Phase 1 if needed

</specifics>

<deferred>
## Deferred Ideas

- PlayableDrumStaff.vue (Tone.js + beat cursor) — v2 requirement, confirmed deferred
- Slide 2 DrumStaff — intentionally left as static image; user supplies a real drum sheet photo
- Additional notation slides beyond slide 9 — NOTE-01 scope is slide 9 only for Phase 2

</deferred>

---

*Phase: 02-vexflow-drum-notation*
*Context gathered: 2026-04-19*
