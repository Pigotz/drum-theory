# Phase 1: Slide Skeleton + Backing Track + Deploy — Research

**Researched:** 2026-04-19
**Domain:** Slidev 52.14.2 + Vue 3 + GitHub Pages deployment
**Confidence:** HIGH

---

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions

- **D-01:** Initialize with `npm create slidev@latest`, then prune demo content and configure for this project. Do not hand-craft package.json from scratch.
- **D-02:** User has neither the backing track nor the drum sheet images. Phase 1 must include stub/placeholder tasks:
  - `public/backing.mp3` — include a placeholder (silence or short tone) so LoopPlayer.vue renders correctly; user will replace with real track before live use
  - Slide 2 drum sheet image — use a Creative Commons drum notation screenshot as placeholder; user will replace
  - Slide 9 image-right panel — user will provide a drum sheet screenshot; Phase 1 scaffolds the slot and documents what's needed
- **D-03:** Render note symbols as hand-crafted SVG files (drawn from scratch). One SVG per note type: whole, half, quarter, eighth, sixteenth note.
- **D-04:** Left column layout: SVG note symbol + note name as a heading (e.g. "Whole Note" above/below the glyph). Audience is notation-naive — the name helps them connect term to symbol.
- **D-05:** Right column (text side of image-right layout): color-coded bullet list showing staff positions — Hi-hat (yellow), Snare (blue), Kick (red).
- **D-06:** Image panel on slide 9: scaffold the image-right slot with a placeholder; user will supply a drum sheet screenshot. Phase 2 replaces the entire slide with DrumStaff.vue.
- **D-07:** Each v-click reveal on slide 11 renders a styled color block card (not plain text). Kick=red bg, Snare=blue bg, Hi-hat=yellow bg; text: white, weight 700.
- **D-08:** Show only Groove 1 (basic rock pattern). No second grid for Groove 2.
- **D-09:** Fold Act 3 transition line into slide 10 as a final line or speaker note. No separate 14th slide.
- **D-10:** Slide 13 — Pure text: "Thanks for being my drum kit." No QR code.
- **D-11:** Slide 1 — Title + subline only. No presenter name/credit.
- **D-12:** Full spoken script on every slide with timing cues. Format: `<!-- note -->` Slidev comment blocks.

### Claude's Discretion

- SVG artistry and exact sizing/proportions of the hand-crafted note symbols
- Whether to use `viewBox` scaling or fixed px dimensions for SVG files
- Exact wording of the full spoken script (tone: informal, funny, drummer-voice)
- Groove grid exact beat count representation (8 cells vs. 16 cells per measure for Groove 1)
- Placeholder backing track implementation (silence stub vs. short generated tone)

### Deferred Ideas (OUT OF SCOPE)

- Groove 2 on slide 12 — could be added as a second grid; noted for possible Phase 1 polish or Phase 2
- QR code on outro slide — optional enhancement; simple to add if presenter wants a link
- Presenter name/credit on cover — easy to add; not needed for friends PowerPoint night
- GitHub Actions auto-deploy (DEPLOY-03) — v2 requirement, out of Phase 1 scope
</user_constraints>

---

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| SLIDE-01 | All 13 slides covering all 4 acts | Slidev `two-cols`, `image`, `image-right`, `statement`, `center`, `cover`, `end`, `quote` layouts — all verified in Context7 |
| SLIDE-02 | Note duration slides (3–7) use v-click reveals | `v-click` directive verified in Context7; right column revealed on first click in `two-cols` layout |
| SLIDE-03 | Act 4 volunteer role cards revealed one at a time | Multiple `v-click` on same slide, sequential — standard Slidev behavior |
| SLIDE-04 | Presenter notes on every slide with timing cues | `<!-- ... -->` comment blocks at slide end — verified in Context7 |
| SLIDE-05 | 12-minute countdown timer in presenter view | `timer: countdown` + `duration: 12min` in headmatter — verified via sli.dev/features/timer |
| NOTE-04 | Groove grid with HTML color grid (kick=red, snare=blue, hi-hat=yellow) | Inline HTML `<table>` in `slides.md` — standard Slidev feature |
| AUDIO-01 | LoopPlayer.vue plays drumless backing track on loop | Plain HTML5 `<audio loop>` + Vue 3 `ref` toggle — no Tone.js needed in Phase 1 |
| AUDIO-02 | LoopPlayer play/pause controlled by button click — no autoplay | HTML5 `<audio>` requires user gesture; `play()` called inside click handler satisfies this |
| DEPLOY-01 | Build with correct base path — no 404s on GitHub Pages | `base: /drum-theory/` in headmatter + `slidev build --base /drum-theory/` |
| DEPLOY-02 | Deployed presentation accessible via GitHub Pages URL | `gh-pages -d dist` script after build; Pages source = gh-pages branch |
</phase_requirements>

---

## Summary

Phase 1 creates a fully scaffolded Slidev presentation from scratch: 13 slides across 4 acts, all interactive elements (v-click reveals, LoopPlayer.vue), and a working GitHub Pages deployment at `pigotz.github.io/drum-theory`. The project codebase is currently empty — the scaffold (`npm create slidev@latest`) is the first task.

The primary risk is the base path integration. Slidev's build output assumes root-relative URLs by default. Without `base: /drum-theory/` in the headmatter AND matching `--base /drum-theory/` on the build command, all assets 404 on GitHub Pages. This must be wired and verified before any slide content is authored.

LoopPlayer.vue uses plain HTML5 `<audio loop>` — no Tone.js in Phase 1. The `await Tone.start()` rule in CLAUDE.md applies only to v2 Tone.js audio handlers and can be ignored here. SVG note symbols are hand-crafted files in `public/svgs/` — one per note type, authored with simple `<ellipse>`, `<line>`, and `<path>` elements. No VexFlow, no fonts, no third-party icon library.

**Primary recommendation:** Scaffold → wire base path → smoke-test deploy → then author slide content in order. Deploy-first removes the highest-risk unknown before 13 slides are written.

---

## Architectural Responsibility Map

| Capability | Primary Tier | Secondary Tier | Rationale |
|------------|-------------|----------------|-----------|
| Slide content and layout | Slidev (slides.md Markdown) | — | Slidev is the renderer; content lives in .md |
| v-click reveal animations | Slidev (built-in directive) | — | No custom JS needed; Slidev handles state |
| Note symbol rendering | Static SVG files (public/) | img tag in slides.md | Simple inline images; no component needed |
| Backing track playback | LoopPlayer.vue (Vue 3 SFC) | HTML5 audio API | Only interactive component in Phase 1 |
| Groove grid display | Inline HTML table in slides.md | — | Static, no data binding needed |
| Presenter notes and timer | Slidev frontmatter + comment blocks | — | Built-in presenter view features |
| Asset path resolution | Vite / `import.meta.env.BASE_URL` | — | Required for GitHub Pages sub-path hosting |
| Deployment | gh-pages npm package | GitHub Pages (gh-pages branch) | Manual deploy per CONTEXT.md D-01 |

---

## Standard Stack

### Core

| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| @slidev/cli | 52.14.2 (locked) | Presentation framework and dev server | Project requirement; Vite-based, hot-reload |
| @slidev/theme-seriph | 0.25.0 | Dark theme for presentations | Locked in project brief; drummer aesthetic |
| vue | 3.x (bundled by Slidev) | Component authoring for LoopPlayer.vue | Bundled with Slidev; no separate install |

### Supporting

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| gh-pages | 6.3.0 | Manual deploy dist/ to gh-pages branch | `npm run deploy` after build |
| tone | 15.1.22 (locked) | AudioContext gate (Phase 2 use only) | Do NOT import in Phase 1 — listed for awareness |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| gh-pages npm | GitHub Actions | Actions is v2 scope (DEPLOY-03); gh-pages is simpler manual workflow |
| Hand-crafted SVG | VexFlow glyphs | VexFlow is Phase 2 scope; SVG files are simpler and match D-03 |
| Plain HTML5 audio | Tone.js player | Tone.js adds AudioContext complexity; plain audio is correct for Phase 1 |

**Installation (after scaffold):**
```bash
npm install -D gh-pages
```

**Version verification (confirmed 2026-04-19):**
```bash
npm view @slidev/cli version      # 52.14.2
npm view @slidev/theme-seriph version  # 0.25.0
npm view gh-pages version         # 6.3.0
```

---

## Architecture Patterns

### System Architecture Diagram

```
slides.md (Markdown)
    │
    ├── headmatter: theme, base, timer, transition
    │       │
    │       └── base: /drum-theory/  ──────────────────────────────┐
    │                                                               │
    ├── Slide 1–13 content                                         │
    │       ├── layouts: cover, two-cols, image, image-right,       │
    │       │            statement, center, quote, end, default     │
    │       ├── v-click directives ──── Slidev click state machine  │
    │       └── <!-- note --> blocks ── Presenter view notes        │
    │                                                               │
    ├── <img src="/svgs/*.svg"> ────── public/svgs/ (5 SVG files)   │
    ├── <LoopPlayer /> ─────────────── components/LoopPlayer.vue    │
    │       │                                 │                     │
    │       │                    HTML5 <audio loop>                 │
    │       │                    src = BASE_URL + 'backing.mp3'     │
    │       │                    public/backing.mp3 (placeholder)   │
    │       │                                                       │
    │       └── aria-label updates on state change                  │
    │                                                               │
    └── Groove grid ─────────────────── inline <table> in slide 12  │
                                                                    │
slidev build --base /drum-theory/ ──────────────────────────────────┘
    │
    └── dist/
            │
            └── gh-pages -d dist ──── GitHub Pages (gh-pages branch)
                                          pigotz.github.io/drum-theory
```

### Recommended Project Structure

```
drum-theory/
├── slides.md              # All 13 slides + headmatter
├── components/
│   └── LoopPlayer.vue     # Play/pause audio component
├── public/
│   ├── backing.mp3        # Placeholder silent MP3 (user replaces)
│   ├── drum-sheet.png     # Placeholder CC image for slide 2 (user replaces)
│   └── svgs/
│       ├── whole-note.svg
│       ├── half-note.svg
│       ├── quarter-note.svg
│       ├── eighth-note.svg
│       └── sixteenth-note.svg
├── package.json           # scripts: dev, build, deploy
└── CLAUDE.md              # (already exists)
```

### Pattern 1: Slidev Headmatter (global config)

**What:** First YAML block in slides.md configures the entire deck.
**When to use:** Always — this is the single source of truth for theme, base path, timer.

```yaml
---
theme: seriph
title: "You Can't Read This"
drawings:
  persist: false
transition: slide-left
base: /drum-theory/
timer: countdown
duration: 12min
---
```

[VERIFIED: sli.dev/features/timer, sli.dev/custom/index via Context7]

### Pattern 2: Slide Separator with Frontmatter

**What:** Three dashes separate slides; frontmatter before content sets per-slide layout.
**When to use:** Every slide needs a layout declaration.

```markdown
---
layout: two-cols
---

# Left Column Content

::right::

<v-click>

Right column content revealed on click

</v-click>
```

[VERIFIED: Context7 /websites/sli_dev — layouts, v-click]

### Pattern 3: v-click on right column only (slides 3–7)

**What:** Wrap the `::right::` slot content in `<v-click>` to reveal on first click while left column is always visible.
**When to use:** Note duration slides — symbol shown on load, analogy on click.

```markdown
---
layout: two-cols
---

<div class="note-symbol">
  <img src="/svgs/whole-note.svg" alt="Whole note symbol" />
  <h2>Whole Note</h2>
</div>

::right::

<v-click>

*The whole pizza — you wait forever*

4 beats. Longest common note. When you see this, count "one-two-three-four" before moving on.

</v-click>
```

[VERIFIED: Context7 /websites/sli_dev — v-click, two-cols layout]

### Pattern 4: Sequential v-click cards (slide 11)

**What:** Three separate `<v-click>` blocks create three sequential reveals.
**When to use:** Volunteer role cards — each click reveals the next card.

```markdown
---
layout: default
---

# Your Roles Tonight

<v-click>
<div style="background: #ef4444; color: white; font-weight: 700; padding: 16px; margin: 8px 0; border-radius: 6px;">
  Chief Kick Officer — BOOM — Beats 1 and 3
</div>
</v-click>

<v-click>
<div style="background: #3b82f6; color: white; font-weight: 700; padding: 16px; margin: 8px 0; border-radius: 6px;">
  Snare Department Head — PAH — Beats 2 and 4
</div>
</v-click>

<v-click>
<div style="background: #eab308; color: white; font-weight: 700; padding: 16px; margin: 8px 0; border-radius: 6px;">
  Hi-Hat Technician — tss — Every eighth note
</div>
</v-click>
```

[VERIFIED: Context7 /websites/sli_dev — v-click animations; colors from CLAUDE.md]

### Pattern 5: Presenter notes format

**What:** HTML comment block at the bottom of a slide becomes presenter notes.
**Critical rule:** Comment must be at the VERY END of the slide content — not in the middle. A comment in the middle is NOT treated as a note.

```markdown
# Slide content here

Some text

<!--
Spoken: "Hey, can you read this? No? Good. That's the point."
Timing: Pause 3 seconds after showing. Let it sink in.
Cue: Move on once you see confused faces.
-->
```

[VERIFIED: Context7 /websites/sli_dev — Presenter Notes Syntax]

### Pattern 6: LoopPlayer.vue (plain HTML5 audio, no Tone.js)

**What:** Minimal Vue 3 SFC with a toggle button and an `<audio loop>` element.
**Critical:** `import.meta.env.BASE_URL` must prefix the audio src at runtime so the path resolves correctly under `/drum-theory/`.
**The `await Tone.start()` rule in CLAUDE.md does NOT apply here** — it governs Tone.js AudioContext initialization only. Plain HTML5 audio does not require it.

```vue
<script setup>
import { ref } from 'vue'

const audio = ref(null)
const playing = ref(false)

const toggle = () => {
  if (playing.value) {
    audio.value.pause()
  } else {
    audio.value.play()
  }
  playing.value = !playing.value
}
</script>

<template>
  <audio
    ref="audio"
    :src="$slidev?.configs?.base ? $slidev.configs.base + 'backing.mp3' : '/backing.mp3'"
    loop
  />
  <button
    @click="toggle"
    :aria-label="playing ? 'Stop backing track' : 'Play backing track'"
    style="
      background: #6366f1;
      color: white;
      font-weight: 700;
      font-size: 14px;
      padding: 12px 16px;
      min-height: 44px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
    "
  >
    {{ playing ? '⏹ Stop' : '▶ Play' }}
  </button>
</template>
```

**Simpler audio src pattern** (per idea.md and CONTEXT.md integration note):

```vue
const src = import.meta.env.BASE_URL + 'backing.mp3'
```

Then use `:src="src"` on the `<audio>` element. `import.meta.env.BASE_URL` resolves to `/drum-theory/` in production build and `/` in dev — correct in both cases. [ASSUMED — standard Vite behavior; verified pattern in CLAUDE.md]

### Pattern 7: Groove grid HTML table (slide 12)

**What:** Static `<table>` with 8-cell beat layout. No interactivity.
**Pattern:**

```html
<table style="border-collapse: collapse; width: 100%;">
  <thead>
    <tr>
      <th style="color: white; font-weight: 700; padding: 8px; text-align: left;"></th>
      <th style="color: white; font-size: 14px; font-weight: 700; padding: 8px; text-align: center;">1</th>
      <th style="color: white; font-size: 14px; font-weight: 700; padding: 8px; text-align: center;">+</th>
      <th style="color: white; font-size: 14px; font-weight: 700; padding: 8px; text-align: center;">2</th>
      <th style="color: white; font-size: 14px; font-weight: 700; padding: 8px; text-align: center;">+</th>
      <th style="color: white; font-size: 14px; font-weight: 700; padding: 8px; text-align: center;">3</th>
      <th style="color: white; font-size: 14px; font-weight: 700; padding: 8px; text-align: center;">+</th>
      <th style="color: white; font-size: 14px; font-weight: 700; padding: 8px; text-align: center;">4</th>
      <th style="color: white; font-size: 14px; font-weight: 700; padding: 8px; text-align: center;">+</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="color: white; font-weight: 700; padding: 8px;">HH</td>
      <td style="background: #eab308; min-width: 24px; padding: 8px;"></td>
      <td style="background: #eab308; min-width: 24px; padding: 8px;"></td>
      <td style="background: #eab308; min-width: 24px; padding: 8px;"></td>
      <td style="background: #eab308; min-width: 24px; padding: 8px;"></td>
      <td style="background: #eab308; min-width: 24px; padding: 8px;"></td>
      <td style="background: #eab308; min-width: 24px; padding: 8px;"></td>
      <td style="background: #eab308; min-width: 24px; padding: 8px;"></td>
      <td style="background: #eab308; min-width: 24px; padding: 8px;"></td>
    </tr>
    <tr>
      <td style="color: white; font-weight: 700; padding: 8px;">SN</td>
      <td style="padding: 8px;"></td>
      <td style="background: #3b82f6; padding: 8px;"></td>
      <td style="padding: 8px;"></td>
      <td style="padding: 8px;"></td>
      <td style="padding: 8px;"></td>
      <td style="background: #3b82f6; padding: 8px;"></td>
      <td style="padding: 8px;"></td>
      <td style="padding: 8px;"></td>
    </tr>
    <tr>
      <td style="color: white; font-weight: 700; padding: 8px;">KK</td>
      <td style="background: #ef4444; padding: 8px;"></td>
      <td style="padding: 8px;"></td>
      <td style="padding: 8px;"></td>
      <td style="padding: 8px;"></td>
      <td style="background: #ef4444; padding: 8px;"></td>
      <td style="padding: 8px;"></td>
      <td style="padding: 8px;"></td>
      <td style="padding: 8px;"></td>
    </tr>
  </tbody>
</table>
```

Groove 1 (basic rock) beat map from idea.md: HH every eighth (all 8 cells), SN on beat 2 and 4 (cells 3 and 7 in 1-indexed), KK on beat 1 and 3 (cells 1 and 5). [VERIFIED: idea.md groove pattern]

### Pattern 8: SVG note symbol construction

**What:** Hand-crafted SVG using basic shapes. Drummer aesthetic — bold, slightly stylized.
**Recommended viewBox:** `0 0 60 100` (portrait, gives room for stem + notehead).

Anatomy summary:
- **Whole note:** Hollow ellipse, no stem. `<ellipse cx="30" cy="70" rx="22" ry="14" fill="none" stroke="white" stroke-width="5"/>`
- **Half note:** Hollow ellipse + vertical stem. Add `<line x1="52" y1="70" x2="52" y2="10" stroke="white" stroke-width="5"/>`
- **Quarter note:** Filled ellipse + vertical stem. Change `fill="white"` (or the slide text color).
- **Eighth note:** Filled ellipse + stem + single curved flag from stem top, curving right.
- **Sixteenth note:** Filled ellipse + stem + two parallel curved flags.

[ASSUMED — standard music notation anatomy; consistent with Study.com lesson and Wikimedia SVG reference]

### Pattern 9: Deploy flow

```bash
# 1. Build with base path
npx slidev build --base /drum-theory/

# 2. Deploy dist/ to gh-pages branch
npx gh-pages -d dist

# 3. Enable Pages in GitHub: Settings → Pages → Source: gh-pages branch → / (root)
```

Add to package.json scripts:
```json
{
  "scripts": {
    "dev": "slidev --open",
    "build": "slidev build --base /drum-theory/",
    "deploy": "npm run build && gh-pages -d dist"
  }
}
```

[VERIFIED: sli.dev/guide/hosting, gh-pages npm package CLI docs via web search]

### Anti-Patterns to Avoid

- **Hardcoding `/backing.mp3` without BASE_URL:** Works in dev, 404s on GitHub Pages. Always use `import.meta.env.BASE_URL + 'backing.mp3'`.
- **Missing `base:` in headmatter:** Build command `--base` flag alone is not sufficient — the headmatter `base:` controls runtime Vue Router paths. Both must match.
- **Using `onMounted`/`onUnmounted` in components:** Slidev preserves component instances across slide navigation. `onMounted` fires once, not on re-entry. Use `onSlideEnter`/`onSlideLeave` from `@slidev/client` for any slide-aware lifecycle logic.
- **Presenter notes not at slide end:** Comment blocks in the middle of slide content are NOT treated as presenter notes by Slidev. Notes must be the last element in the slide block.
- **Calling `Tone.start()` in Phase 1 audio handler:** LoopPlayer.vue uses plain HTML5 audio — `Tone.start()` is irrelevant and adds an unnecessary import. Leave Tone.js out entirely until Phase 2.
- **`v-click` on entire two-cols slide:** Wrap only the right column content in `<v-click>`, not the whole slide. The left column (note symbol) must be visible on load.

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Slide transitions and animation state | Custom JS click counter | Slidev `v-click` directive | Slidev manages click state, history, presenter sync |
| Presenter view + speaker notes UI | Custom overlay component | Slidev built-in presenter view | Built-in supports notes, timer, next-slide preview |
| Static file server for dev | Custom server | `slidev dev` (Vite HMR) | Hot-reload, handles base path correctly in dev |
| GitHub Pages routing | Custom 404.html hacks | `base:` frontmatter + Slidev SPA build | Slidev's build is already SPA-optimized |
| Audio path resolution under sub-path | String concatenation heuristics | `import.meta.env.BASE_URL` | Vite resolves this correctly in both dev and prod |
| Note glyph fonts | Load SMuFL/Bravura font | Hand-crafted SVG per D-03 | Decision D-03 locks this; avoids font loading complexity |

**Key insight:** Slidev handles nearly all presentation-layer complexity. The implementation work in Phase 1 is mostly content authoring (slides.md) and one small Vue component (LoopPlayer.vue).

---

## Common Pitfalls

### Pitfall 1: Double base path mismatch

**What goes wrong:** Assets 404 on GitHub Pages or navigation breaks.
**Why it happens:** `base:` in headmatter and `--base` on build command must match exactly (`/drum-theory/`). If one is set and the other is not, either the build output is wrong or the runtime router is wrong.
**How to avoid:** Set both. Headmatter: `base: /drum-theory/`. Build command: `slidev build --base /drum-theory/`. The `deploy` npm script should always call `slidev build --base /drum-theory/` — never bare `slidev build`.
**Warning signs:** Opening the GitHub Pages URL shows a blank page or 404; browser console shows 404s for JS/CSS chunks.

### Pitfall 2: npm create slidev@latest generates demo content

**What goes wrong:** Demo slides.md contains multiple example slides, font imports, and component examples that pollute the project.
**Why it happens:** The scaffold generates a full starter template, not a blank project.
**How to avoid:** After scaffolding, replace `slides.md` with the project headmatter + 13 blank slide stubs immediately. Do not author content on top of demo slides.
**Warning signs:** The dev server shows Slidev's starter slides instead of the project structure.

### Pitfall 3: GitHub Pages source not configured

**What goes wrong:** `npm run deploy` pushes to `gh-pages` branch successfully, but the URL returns 404.
**Why it happens:** GitHub Pages must be explicitly enabled in repo settings. The `gh-pages` branch existing does not automatically activate Pages.
**How to avoid:** After first deploy: Settings → Pages → Source: `Deploy from a branch` → Branch: `gh-pages` → `/ (root)` → Save.
**Warning signs:** `gh-pages` branch appears in GitHub UI but `pigotz.github.io/drum-theory` returns 404.

### Pitfall 4: Presenter notes comment not at slide end

**What goes wrong:** Comment appears in the slide body as visible text, not in presenter view.
**Why it happens:** Slidev only treats a comment block as notes if it is the last thing in the slide block. A comment between content elements is rendered as content.
**How to avoid:** Always place `<!-- ... -->` note blocks as the absolute last element before the `---` separator.
**Warning signs:** Presentation mode shows comment text visible on slide.

### Pitfall 5: HTML5 audio autoplay policy

**What goes wrong:** `audio.value.play()` throws `DOMException: play() failed because the user didn't interact with the document first`.
**Why it happens:** Browsers block `play()` calls not triggered by direct user gesture.
**How to avoid:** The `@click` handler on the LoopPlayer button constitutes a user gesture — this is correct. Do NOT call `play()` in `onMounted`, `onSlideEnter`, or any lifecycle hook. Only call it inside the button click handler.
**Warning signs:** Console shows `NotAllowedError` or `DOMException` on audio play.

### Pitfall 6: SVG files not in public/ — broken references in build

**What goes wrong:** `<img src="/svgs/whole-note.svg">` resolves to 404 in production.
**Why it happens:** In Slidev (Vite-based), files in `public/` are served as-is at the root. Files referenced with a leading `/` are served relative to `BASE_URL`. If SVGs are outside `public/`, they won't be included in the build output.
**How to avoid:** All static assets (SVGs, MP3, images) must live in `public/`. Reference them as `import.meta.env.BASE_URL + 'svgs/whole-note.svg'` or in Markdown as `/svgs/whole-note.svg` (Slidev rewrites these during build with the correct base).
**Warning signs:** Images visible in `slidev dev` but 404 after `npm run deploy`.

---

## Placeholder Backing Track Strategy

ffmpeg is NOT available on this machine. Sox is NOT available. The simplest approach without external tools:

**Option A — Python WAV stub (recommended):** Python 3 `wave` module is available. Generate a 1-second silent WAV, then note that browsers accept WAV files for `<audio>`. However, CLAUDE.md specifies `.mp3`. A WAV placeholder will work for development and the user replaces it with their real MP3 before presentation.

```python
import wave, struct
with wave.open('public/backing.mp3', 'w') as f:
    # Note: naming it .mp3 while writing WAV works as placeholder
    # User replaces with real MP3 before live use
    f.setnchannels(1)
    f.setsampwidth(2)
    f.setframerate(44100)
    f.writeframes(struct.pack('<44100h', *([0] * 44100)))
```

[ASSUMED — WAV-as-placeholder approach; browsers typically accept WAV in audio elements but file naming may cause type mismatch issues]

**Option B — Minimal valid MP3 bytes (recommended for correctness):** A tiny pre-known byte sequence for a valid ~0.1s silent MP3 frame can be written directly as a Node.js buffer. This avoids the WAV/MP3 confusion.

```js
// scripts/gen-placeholder.js
const fs = require('fs')
// A valid 1-frame silent MPEG1 Layer3 MP3 header + silent frame
// ID3v2 + one silent 128kbps frame (~26 bytes)
const silentFrame = Buffer.from(
  'FFFB9000000000000000000000000000000000000000000000000000000000000000',
  'hex'
)
fs.mkdirSync('public', { recursive: true })
fs.writeFileSync('public/backing.mp3', silentFrame)
```

[ASSUMED — MP3 frame structure; the exact byte sequence needs verification against the MPEG spec. The safest approach without ffmpeg is to download a 1-second silent MP3 from a known source like github.com/anars/blank-audio]

**Option C — Download from blank-audio repo (safest):** The `anars/blank-audio` GitHub repository provides ready-made blank MP3 files. Use `gh` CLI or `curl` to download `1-second.mp3`. [VERIFIED: exists at https://github.com/anars/blank-audio via web search]

**Recommendation:** Use Option C in the plan task — `curl` or `gh` download of a known silent MP3 stub. Simplest, avoids byte-level guesswork.

---

## Slide Content Map

All 13 slides and their layouts, from idea.md + CONTEXT.md + UI-SPEC:

| # | Slide | Layout | Key Content | Special |
|---|-------|--------|-------------|---------|
| 1 | Cover | `cover` | "You Can't Read This" / "A drummer's guide to the language nobody asked for" | D-11: no presenter credit |
| 2 | Hook | `image` | Full-screen drum sheet placeholder | CC image placeholder; user replaces |
| 3 | Whole note | `two-cols` | Left: whole-note.svg + "Whole Note" / Right: pizza analogy (v-click) | D-03, D-04 |
| 4 | Half note | `two-cols` | Left: half-note.svg + "Half Note" / Right: pizza analogy (v-click) | |
| 5 | Quarter note | `two-cols` | Left: quarter-note.svg + "Quarter Note" / Right: pizza analogy (v-click) | |
| 6 | Eighth note | `two-cols` | Left: eighth-note.svg + "Eighth Note" / Right: pizza analogy (v-click) | |
| 7 | Sixteenth note | `two-cols` | Left: sixteenth-note.svg + "Sixteenth Note" / Right: pizza analogy (v-click) | D-09: note escape hatch in speaker notes |
| 8 | Key insight | `center` | "The beat doesn't speed up. You just fit more sounds inside the same time." | Display 48px |
| 9 | Drum notation | `image-right` | Left: color-coded bullets / Right: placeholder image | D-05, D-06 |
| 10 | Notation quote | `statement` | "This isn't a melody. It's a map of what each limb does simultaneously." + Act 3 transition line | D-09 |
| 11 | Volunteer roles | `default` | 3 color cards via v-click | D-07 |
| 12 | Groove grid | `two-cols` | Left: HTML color table / Right: LoopPlayer.vue | D-08 |
| 13 | Outro | `end` | "Thanks for being my drum kit." | D-10: no QR code |

---

## Assumptions Log

| # | Claim | Section | Risk if Wrong |
|---|-------|---------|---------------|
| A1 | `import.meta.env.BASE_URL` resolves to `/drum-theory/` in production Slidev build (standard Vite behavior) | Patterns #6, #8 | Audio src and SVG paths 404 on GitHub Pages |
| A2 | WAV file written with `.mp3` extension works as placeholder in `<audio>` element during dev | Placeholder strategy Option A | Browser rejects audio, LoopPlayer button has no effect |
| A3 | SVG note anatomy (whole=hollow ellipse, half=hollow+stem, etc.) matches standard notation conventions | Pattern #8 | Visual symbols are unrecognizable to audience |
| A4 | `gh-pages -d dist` deploys correctly without additional `--dotfiles` or `--nojekyll` flags | Pattern #9 | Build artifacts may be gitignored or Jekyll may interfere |
| A5 | Slidev seriph theme 0.25.0 supports all 8 built-in layouts (cover, two-cols, image, image-right, statement, center, quote, end) | Slide Content Map | Some layouts may require manual layout files |

---

## Open Questions

1. **`--nojekyll` flag needed for gh-pages deploy?**
   - What we know: GitHub Pages runs Jekyll by default; Vite build output includes JS files with underscores that Jekyll ignores.
   - What's unclear: Does `gh-pages` automatically create `.nojekyll` or does it need to be added to `dist/` before deploy?
   - Recommendation: Add `touch dist/.nojekyll` step before `gh-pages -d dist` in the deploy script to be safe. [ASSUMED — standard practice for SPA deployments to GitHub Pages]

2. **Slidev `quote` vs `statement` layout for slide 10**
   - What we know: UI-SPEC says `statement` for slide 10. idea.md says `quote` or `statement`. Context7 confirms both exist.
   - What's unclear: Visual difference between the two in seriph dark theme.
   - Recommendation: Use `statement` per UI-SPEC (D-09 folds the transition line in, making it a statement rather than a quoted attribution).

3. **`base:` in headmatter vs `--base` in build — are both required?**
   - What we know: Official GitHub Actions workflow uses only `--base` flag. The `base:` headmatter is documented separately.
   - What's unclear: Whether `base:` headmatter is required at all when `--base` is passed to the build command, or if one drives the other.
   - Recommendation: Set both to be safe — headmatter `base: /drum-theory/` AND `slidev build --base /drum-theory/`. [ASSUMED — belt-and-suspenders approach; test after first deploy]

---

## Environment Availability

| Dependency | Required By | Available | Version | Fallback |
|------------|------------|-----------|---------|----------|
| Node.js | Slidev dev/build | Yes | v24.14.1 | — |
| npm | Package install | Yes | 11.11.0 | — |
| git | Version control, gh-pages push | Yes | 2.43.0 | — |
| gh CLI | GitHub Pages setup verification | Yes | 2.89.0 | Manual browser settings |
| ffmpeg | Silent MP3 placeholder generation | No | — | Python wave module (WAV) or download from blank-audio repo |
| sox | Alternative audio generation | No | — | Same as above |
| Python 3 | Placeholder MP3 generation | Yes | Available (wave, audioop confirmed) | — |
| @slidev/cli@52.14.2 | Presentation dev/build | Not installed (project empty) | Will install via scaffold | — |
| @slidev/theme-seriph@0.25.0 | Slide theming | Not installed | Will install via scaffold | — |
| gh-pages@6.3.0 | Manual deploy | Not installed | `npm install -D gh-pages` | — |

**GitHub Pages status:** Repository `pigotz/drum-theory` exists on GitHub (confirmed). Pages is NOT yet configured (confirmed via GitHub API 404). `gh-pages` branch does not yet exist. Both will be created during Phase 1 deploy task.

**Missing dependencies with fallback:**
- ffmpeg: Use Python `wave` module or download silent MP3 from `anars/blank-audio` repo

**Missing dependencies with no fallback:**
- None — all blocking tools are available

---

## Security Domain

Security enforcement is not a concern for this phase. This is a local-first presentation tool with no user input, no authentication, no server-side code, and no sensitive data. The only external dependency is GitHub Pages static hosting.

ASVS categories V2 (authentication), V3 (session), V4 (access control), V6 (cryptography) do not apply. V5 (input validation) is trivially satisfied — no user input exists.

---

## Sources

### Primary (HIGH confidence)
- Context7 `/websites/sli_dev` — frontmatter, v-click, layouts, presenter notes, lifecycle hooks, GitHub Pages workflow
- `sli.dev/features/timer` — `timer: countdown`, `duration: 12min` headmatter syntax
- `idea.md` — slide breakdown, groove patterns, pizza analogy copy, LoopPlayer pattern
- `CLAUDE.md` — stack versions, base path rule, audio path pattern, color codes
- `.planning/01-slide-skeleton-backing-track-deploy/01-CONTEXT.md` — all locked decisions
- `.planning/01-slide-skeleton-backing-track-deploy/01-UI-SPEC.md` — layout contract, interaction contract, copy contract

### Secondary (MEDIUM confidence)
- npm registry `npm view` — verified package versions (@slidev/cli 52.14.2, @slidev/theme-seriph 0.25.0, gh-pages 6.3.0, tone 15.1.22)
- Web search + sli.dev/guide/hosting — gh-pages manual deploy workflow (`gh-pages -d dist`)
- GitHub API — confirmed `pigotz/drum-theory` repo exists; confirmed Pages not yet configured; confirmed only `main` branch exists

### Tertiary (LOW confidence)
- Web search — `anars/blank-audio` as silent MP3 source (not verified by visiting the repo)
- Training knowledge — SVG note anatomy (standard music notation); MP3 byte structure for placeholder generation

---

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH — all versions verified via npm registry
- Architecture patterns: HIGH — layouts, v-click, presenter notes all verified via Context7/official docs
- Deploy workflow: HIGH — sli.dev/guide/hosting + gh-pages web search
- SVG note construction: MEDIUM — anatomy correct per music theory, specific SVG paths are discretionary
- Placeholder MP3 strategy: LOW — depends on available tools; multiple fallback paths documented

**Research date:** 2026-04-19
**Valid until:** 2026-05-19 (Slidev is stable; seriph 0.25.0 pinned)
