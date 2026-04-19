# Stack Research: Drum Theory Presentation

**Project:** You Can't Read This — Drum Theory Presentation
**Researched:** 2026-04-19
**Confidence:** HIGH (versions), MEDIUM (VexFlow percussion API specifics)

---

## Recommended Stack

| Library | Pin To | Reason |
|---------|--------|--------|
| `@slidev/cli` | `52.14.2` | Current stable (version scheme changed from 0.x to 52.x in June 2025) |
| `@slidev/theme-seriph` | `0.25.0` | Project requirement, no peer deps |
| `vexflow` | `5.0.0` | Current stable, ESM-first (last v4 was 4.2.5) |
| `tone` | `15.1.22` | `latest` tag stable; 15.5.x is `next` pre-release — avoid |

---

## Slidev

**Version:** `@slidev/cli@52.14.2` (2026-04-05). Vue `^3.5.29` + Vite `^7.3.1`. Node `>=18`.

**Key facts:**
- The version jumped from `0.x` to `52.x` in June 2025 — versioning scheme change, not a breaking release.
- The `next` dist-tag (`0.14.0-beta.14`) is a completely separate pre-release stream — ignore it.
- Component authoring unchanged: drop `.vue` files in `components/`, they auto-register via `unplugin-vue-components`.

**Critical lifecycle gotcha:**
`onMounted` fires only once per component instance, not on slide re-entry. Use `onSlideEnter` / `onSlideLeave` from `@slidev/client` for any slide-level logic (VexFlow re-render, Tone.js Transport stop-on-leave). This is the #1 Slidev-specific trap.

---

## VexFlow

**Version:** `vexflow@5.0.0` (2025-03-05). ESM-first. Repo now at `vexflow/vexflow` GitHub org.

**Key facts:**
- Percussion notation confirmed supported: x-noteheads, percussion clef, multi-voice layouts.
- Color coding: `note.setKeyStyle(keyIndex, { fillStyle: 'red', strokeStyle: 'red' })`.
- **Use SVG backend over Canvas** — SVG survives Slidev's export/PDF pipeline and scales with CSS. Canvas is rasterized, loses resolution, requires explicit pixel dimensions.
- Two-voice layout: stems-up voice (hi-hat/snare) and stems-down voice (kick) formatted together via `Formatter`.

**Confidence: MEDIUM for percussion API** — percussion support is confirmed but the exact x-notehead call signatures and `addClef('percussion')` behavior were not verified with working code examples. **Budget a 30-60 min spike before building the component API.** Don't assume v5 percussion API matches v3/v4 tutorials.

---

## Tone.js

**Version:** `tone@15.1.22` (npm `latest` tag). `tone@15.5.6` is on `next` pre-release — avoid.

**Key facts:**
- Dependencies: only `standardized-audio-context` + `tslib`.
- Beat cursor sync: use `Tone.getDraw().schedule(() => { /* update reactive state */ }, time)` inside a `Transport.scheduleRepeat` callback — bridges audio thread to animation frame without drift. Do NOT use `setInterval`.
- `await Tone.start()` must be called inside a click handler before any playback. Browser requirement, not Tone.js limitation. The play button is the required gesture.

**Unresolved:** `Tone.getDraw()` vs `Tone.Draw` — API name may differ in `15.1.22`. Verify against installed version before Phase 3.

---

## GitHub Pages Deployment

**Official Slidev GitHub Actions workflow** is confirmed and ready to use verbatim.

**Build command:** `slidev build --base /${{ github.event.repository.name }}/`
(auto-resolves to `/drum-theory/`)

**Critical runtime asset path trap:**
Vite does NOT rewrite asset paths in Tone.js `Player` URLs at runtime. Use `import.meta.env.BASE_URL + 'samples/kick.wav'` to construct audio file paths — not bare `/samples/kick.wav`. Otherwise audio will 404 in production under `/drum-theory/` even though static files work.

Static assets in `public/` referenced from `<audio src="...">` HTML attributes are handled correctly by Slidev's build.

**GitHub Pages source:** Must be set to "GitHub Actions" in repo Settings → Pages (not the `gh-pages` branch option).

---

## Confidence Assessment

| Area | Level | Reason |
|------|-------|--------|
| Slidev version + deploy | HIGH | npm registry + official docs confirmed |
| Slidev component/lifecycle patterns | HIGH | `onSlideEnter`/`onSlideLeave` confirmed from official docs |
| VexFlow version | HIGH | npm registry confirmed |
| VexFlow percussion API | MEDIUM | Support confirmed; x-notehead call signatures not verified with working examples |
| Tone.js version + scheduling | HIGH | npm registry + official docs confirmed Transport, Sequence, Draw patterns |
| Tone.js runtime asset paths | MEDIUM | Known Vite behavior; needs verification in subdirectory deploy |

---

## Open Questions for Phase-Specific Research

1. Does `addClef('percussion')` in VexFlow 5 render a percussion clef glyph correctly?
2. Exact VexFlow 5 API for x-noteheads — `duration` suffix (`'qx'`), `NoteHead` type enum, or key notation convention?
3. Is the cursor sync API `Tone.getDraw()` or `Tone.Draw` in `tone@15.1.22`?
4. Runtime asset path construction with `import.meta.env.BASE_URL` — verify works correctly in Slidev's Vite config for subdirectory deploys.
