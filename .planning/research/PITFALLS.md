# Domain Pitfalls

**Domain:** Slidev presentation with VexFlow drum notation and Tone.js audio, deployed to GitHub Pages
**Researched:** 2026-04-19
**Confidence:** HIGH (all critical pitfalls verified against official documentation or Context7 sources)

---

## Critical Pitfalls

### Pitfall 1: Base Path Mismatch Breaks All Assets on GitHub Pages

**What goes wrong:** The deployed site at `username.github.io/drum-theory/` loads a blank page or throws 404s for all JS/CSS/image/audio assets. Everything works locally but nothing works in production.

**Why it happens:** Slidev's default base is `/`. When GitHub Pages serves the site under a repository subdirectory (`/drum-theory/`), every asset reference becomes an absolute path starting with `/` — pointing at the root domain, not the subdirectory. The fix must be applied in two places and they must stay in sync.

**Consequences:** Complete build failure on deployment. No fallback — the entire presentation is unusable.

**Prevention:**
1. Set `base: /drum-theory/` in `slides.md` frontmatter.
2. Pass `--base /drum-theory/` to the build command. The GitHub Actions workflow template from Slidev does this automatically via `--base /${{ github.event.repository.name }}/`.
3. All audio/image files in `public/` must be referenced with leading slash only (`/backing.mp3`, not `./backing.mp3`). Relative paths in frontmatter and component props break after build.

**Warning signs:**
- Any asset URL in browser DevTools that does NOT start with `/drum-theory/` after build.
- Running `slidev build` without `--base` and then pushing.
- Using `src="./backing.mp3"` in a Vue component instead of `src="/backing.mp3"`.

**Detection:** Run `slidev build --base /drum-theory/` locally and inspect `dist/index.html` before pushing.

**Phase:** Deployment setup. Fix once with the GitHub Actions template.

---

### Pitfall 2: VexFlow Renderer Instantiated Before the DOM Element Exists

**What goes wrong:** `DrumStaff.vue` renders nothing, or throws `Cannot read properties of null` on mount.

**Why it happens:** VexFlow requires a real DOM node at instantiation time: `new Renderer(div, Renderer.Backends.SVG)`. If this runs in `setup()` or `created()`, the `ref` is still `null`. Slidev adds an additional wrinkle: components may be pre-rendered in background slide slots before they are actually visible.

**Consequences:** Silent blank output or a JavaScript error. May only appear on certain slides or after navigating back, making it hard to reproduce.

**Prevention:**
- All VexFlow initialization inside `onMounted()`. Guard against null: `if (!container.value) return`.
- Use `onSlideEnter` from `@slidev/client` for components that need to re-render when navigating back to a slide.
- Prefer `Renderer.Backends.SVG` — SVG renders into a `div` and scales with CSS. Canvas requires explicit pixel dimensions and does not respond to `width: 100%`.

**Warning signs:**
- Component works in a plain Vue app but is blank inside Slidev.
- Notation only appears after a hard refresh, not on initial slide load.

**Phase:** Phase 2 — VexFlow static notation.

---

### Pitfall 3: Tone.js AudioContext Suspended — Audio Silently Does Nothing

**What goes wrong:** Clicking play produces no audio, no error, no feedback.

**Why it happens:** All modern browsers start the Web Audio API `AudioContext` in a suspended state. Calling `Tone.Transport.start()` before a user gesture has resolved `Tone.start()` silently no-ops — no exception thrown. Mobile browsers are especially strict.

**Consequences:** The playable notation feature works in development (where the developer has already interacted with the page) but fails in demo conditions.

**Prevention:**
- Call `await Tone.start()` as the first line inside every click handler that triggers audio. Not in `onMounted`.
- Check `Tone.context.state` before starting transport: if `"suspended"`, `Tone.start()` has not been called.
- The play button is the sufficient gesture — no separate "initialize audio" step needed.

**Warning signs:**
- `Tone.context.state === "suspended"` in DevTools console.
- Audio works in local dev but not on the GitHub Pages deployment (fresh page load, no prior interaction).

**Phase:** Phase 3 — playable notation. Address on day one of that phase.

---

### Pitfall 4: VexFlow Percussion Notation Requires Non-Obvious Configuration

**What goes wrong:** Drum notes render on a treble clef with filled oval noteheads at wrong staff positions. Looks like broken piano music, not drum notation.

**Why it happens:** VexFlow defaults to treble clef with standard oval noteheads. Percussion notation requires three changes not prominently documented:
1. `stave.addClef('percussion')` — not `'treble'`.
2. `noteType: 'x'` in `StaveNote` for hi-hat x-shaped noteheads.
3. Staff positions are fixed by convention, not pitch. The position map is manual — there is no built-in drum map.
4. Stem direction must be set explicitly for multi-voice patterns.

**Consequences:** Hours lost trying to make notation look right. The percussion clef absence is immediately obvious.

**Prevention:**
- Spike a single working bar with one correct kick, snare, and hi-hat note before building the component API.
- Hardcode the three-voice pattern as a known working example, then parameterize it.
- Approximate starting positions: `{ hihat: 'a/5', snare: 'c/5', kick: 'c/4' }` — verify empirically in spike.

**Warning signs:**
- All noteheads are oval (no x for hi-hat).
- No percussion clef symbol displayed.

**Phase:** Phase 2 — resolve in initial spike before building component API.

---

## Moderate Pitfalls

### Pitfall 5: Tone.js Samples Not Loaded When Play Is Clicked

**What goes wrong:** User clicks play, nothing happens. The audio buffer has not finished loading.

**Prevention:**
- Disable the play button until `Tone.loaded()` resolves. Update a Vue `ref` via the `onload` callback.
- Keep sample files small: individual drum hits should be under 100 KB each (WAV preferred for short percussive sounds). Backing track as MP3.
- Place all samples in `public/samples/` and reference with absolute paths `/samples/kick.wav`.

**Phase:** Phase 3.

---

### Pitfall 6: VexFlow Canvas Sizing is Fixed Pixels — Breaks Responsive Layout

**What goes wrong:** The notation stave overflows the slide or is clipped. CSS `width: 100%` has no effect.

**Prevention:**
- Use `Renderer.Backends.SVG` exclusively. SVG output goes into a `div` and can be constrained with `max-width: 100%; height: auto`.
- For Slidev's fixed 16:9 slide layout, hardcoding a width like `800` for the SVG renderer is acceptable.

**Phase:** Phase 2.

---

### Pitfall 7: Tone.js Transport State Leaks Between Slide Navigations

**What goes wrong:** Navigating away from a notation slide and back causes double-playback or the beat cursor is out of sync.

**Why it happens:** `Tone.Transport` is a global singleton. If `PlayableDrumStaff.vue` starts the Transport and does not stop it on unmount, the Transport keeps ticking.

**Prevention:**
- In `onBeforeUnmount`, call `Tone.Transport.stop()` and `Tone.Transport.cancel()`.
- Dispose any `Tone.Player` or `Tone.Sequence` instances.
- Also handle `onSlideLeave` from `@slidev/client` — fires before slide animates out.

**Phase:** Phase 3.

---

### Pitfall 8: The LoopPlayer Audio Source Path Breaks on GitHub Pages

**What goes wrong:** Backing track plays in dev but 404s on the deployed site.

**Prevention:**
- Place the backing track in `public/` and reference as `/backing.mp3`. When `--base /drum-theory/` is passed at build time, Slidev/Vite handles the path correctly for files in `public/`.
- Verify by inspecting network requests on the deployed site immediately after first deploy.

**Warning signs:**
- `404 /backing.mp3` (not `/drum-theory/backing.mp3`) in DevTools Network tab on deployed site.

**Phase:** Phase 1 — verify during first deployment.

---

## Minor Pitfalls

### Pitfall 9: Drum Sample Licensing and File Size

**Prevention:**
- Use Freesound.org with CC0 or CC BY filter. Download only three files: kick, snare, hi-hat. Total under 600 KB.
- For testing, use Tone.js's own sample library (`tonejs.github.io/audio/`) — open license, CDN-served. Switch to local files before the actual presentation.
- Do NOT use Splice or commercial sample pack files in a publicly-deployed project.

**Phase:** Phase 3 — sample acquisition step.

---

### Pitfall 10: Playable Notation Feature Eats the Timeline

**What goes wrong:** Phase 3 is still incomplete on the day of the presentation.

**Why it happens:** Audio-visual synchronization (beat cursor + Tone.js + VexFlow) is routinely underestimated.

**Prevention:**
- Treat Phase 3 as a cut feature from day one.
- Set a hard decision point: if Phase 3 is not working cleanly 48 hours before the presentation, cut it. Static VexFlow staves are already visually superior to screenshots.
- Do not start Phase 3 until Phase 1 and Phase 2 are deployed and verified.

**Phase:** All phases — scope management from day one.

---

## Phase-Specific Warning Map

| Phase | Topic | Likely Pitfall | Mitigation |
|-------|-------|----------------|------------|
| Phase 1 (Backing Track) | LoopPlayer audio source | Asset path 404 on GitHub Pages (Pitfall 8) | Verify on first deploy immediately |
| Phase 1 (Backing Track) | Native `<audio>` autoplay | Browser blocks without gesture | Toggle button IS the gesture — no extra work |
| Phase 1 (Deploy) | GitHub Actions workflow | Base path not passed to build | Use Slidev's official workflow template verbatim |
| Phase 2 (VexFlow) | Renderer instantiation | DOM not ready (Pitfall 2) | All VexFlow code inside `onMounted`, guard null ref |
| Phase 2 (VexFlow) | Percussion clef | Non-obvious config (Pitfall 4) | Spike one working bar before building component API |
| Phase 2 (VexFlow) | Canvas vs SVG | Fixed pixels break layout (Pitfall 6) | Use SVG backend exclusively |
| Phase 3 (Playable) | AudioContext state | Suspended context, silent failure (Pitfall 3) | `await Tone.start()` at top of every click handler |
| Phase 3 (Playable) | Sample loading | Buffer not ready (Pitfall 5) | Disable play button until `Tone.loaded()` resolves |
| Phase 3 (Playable) | Transport lifecycle | State leaks between slides (Pitfall 7) | Stop and cancel in `onBeforeUnmount` and `onSlideLeave` |
| Phase 3 (Playable) | Sample files | Licensing or size (Pitfall 9) | CC0 only, three files, under 600 KB total |
| All phases | Scope | Playable notation incomplete (Pitfall 10) | Hard cut decision 48 hours before presentation |
