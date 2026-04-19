# Architecture Research: Drum Theory Presentation

**Project:** You Can't Read This — Drum Theory Presentation
**Confidence:** HIGH — verified against official Slidev, VexFlow, and Tone.js sources

---

## Component Structure

```
slides.md
├── components/
│   ├── LoopPlayer.vue           ← Phase 1: HTML5 <audio>, no library deps
│   ├── DrumStaff.vue            ← Phase 2: VexFlow SVG renderer, DrumPattern prop
│   └── PlayableDrumStaff.vue   ← Phase 3: DrumStaff + Tone.js + beat cursor
├── public/
│   ├── backing.mp3
│   └── samples/{kick,snare,hihat}.wav
└── .github/workflows/deploy.yml
```

## Data Flow

**DrumPattern** is the single source of truth for both VexFlow and Tone.js:

```typescript
interface DrumPattern {
  steps: number       // 8 for eighth-note grid
  bpm: number
  hihat: boolean[]   // true = hit, false = rest
  snare: boolean[]
  kick:  boolean[]
}
```

Same array drives VexFlow note positions AND Tone.Sequence scheduling. No shared global state. No Pinia. No composables directory needed.

## Component Boundaries

| Component | Owns | Does Not Own |
|-----------|------|--------------|
| LoopPlayer | HTML5 Audio playback, play/pause state | Tone.js, VexFlow, pattern data |
| DrumStaff | VexFlow render lifecycle, note coloring, voice formatting | Audio, Transport, cursor |
| PlayableDrumStaff | Tone.js init, Player loading, Sequence scheduling, cursor position | LoopPlayer backing track |

## Build Order (hard dependencies)

1. **slides.md skeleton** — establishes Slidev config and slide structure
2. **LoopPlayer.vue** — zero external deps, proves Slidev custom component pipeline works
3. **DrumStaff.vue** — locks in DrumPattern interface; VexFlow must be installed first
4. **PlayableDrumStaff.vue** — depends on DrumPattern being stable, samples present in public/
5. **deploy.yml** — can be wired anytime, independent of component work

## Key Architectural Facts

### Slidev component auto-import
Any `.vue` file in `components/` is automatically registered (powered by `unplugin-vue-components` bundled with Slidev). Usage: `<DrumStaff :pattern="..." />` directly in Markdown — no import statements needed.

### Lifecycle hooks — critical trap
Slidev preserves component instances across slides. `onMounted`/`onUnmounted` do NOT fire on slide navigation. Use `onSlideEnter`/`onSlideLeave` from `@slidev/client` for slide-level logic. Concretely: **Tone.js Transport must be stopped in `onSlideLeave`**, not `onUnmounted`.

### VexFlow two-voice drum staves
Hi-hat/snare (stems up) and kick (stems down) must be separate Voice objects formatted together. Each `false` step becomes a rest note of the same duration — keeps Voice tick count balanced so Formatter doesn't throw. Color coding: `note.setStyle({ fillStyle: '#ef4444', strokeStyle: '#ef4444' })` before draw.

### Tone.js autoplay constraint (hard)
`await Tone.start()` must be called inside a click handler — no exceptions. All Tone.js init must be gated behind the resolved promise. `Tone.context.state`: `'suspended'` before, `'running'` after. Scheduling before context runs produces silent failure with no error — very hard to debug.

### GitHub Actions deploy
Official Slidev workflow uses `@antfu/ni` for package manager agnosticism, builds with `--base /${{github.event.repository.name}}/` (auto-resolves to `/drum-theory/`), deploys via `actions/deploy-pages@v4`. GitHub Pages source must be set to "GitHub Actions" (not the gh-pages branch option).

## Critical Anti-Patterns

| Anti-Pattern | Consequence | Fix |
|---|---|---|
| Using `onUnmounted` to stop Tone.js | Transport plays on other slides | Use `onSlideLeave` from `@slidev/client` |
| Scheduling before `Tone.start()` resolves | Silent failure, hard to debug | Gate all Tone init behind `await Tone.start()` in click handler |
| Not clearing `innerHTML` before VexFlow re-render | Stacked duplicate staves | Always `staffRef.value.innerHTML = ''` at render start |
| Hardcoding `/drum-theory/` in component src | 404s during local dev | Use `import.meta.env.BASE_URL` or pass src as prop |

## Open Questions for Phase-Specific Research

| Phase | Question |
|-------|----------|
| Phase 2 | Exact VexFlow key strings for drum staff positions and x-notehead field name — percussion docs sparse |
| Phase 3 | `Tone.getDraw()` vs `Tone.Draw` naming — changed between v14/v15, verify against installed version |
| Phase 3 | `note.getAbsoluteX()` requires note inside formatted, drawn Voice — verify accessibility after `vf.draw()` |
| All | Seriph dark theme + VexFlow SVG fills — explicit color values should hold, needs visual confirmation |
