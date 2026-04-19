# Phase 1: Slide Skeleton + Backing Track + Deploy - Context

**Gathered:** 2026-04-19
**Status:** Ready for planning

<domain>
## Phase Boundary

A complete, deployable Slidev presentation that can run a live Human Drum session. Delivers all 13 slides across 4 acts, LoopPlayer.vue backing track component, and a working GitHub Pages deployment. VexFlow notation is Phase 2 scope — Phase 1 uses color-coded bullet lists and placeholder images where Phase 2 will slot in.

</domain>

<decisions>
## Implementation Decisions

### Project Scaffold
- **D-01:** Initialize with `npm create slidev@latest`, then prune demo content and configure for this project. Do not hand-craft package.json from scratch.

### Asset Sourcing
- **D-02:** User has neither the backing track nor the drum sheet images. Phase 1 must include stub/placeholder tasks:
  - `public/backing.mp3` — include a placeholder (silence or short tone) so LoopPlayer.vue renders correctly; user will replace with real track before live use
  - Slide 2 drum sheet image — use a Creative Commons drum notation screenshot as placeholder; user will replace
  - Slide 9 image-right panel — user will provide a drum sheet screenshot; Phase 1 scaffolds the slot and documents what's needed

### Note Duration Symbols (Slides 3–7)
- **D-03:** Render note symbols as hand-crafted SVG files (drawn from scratch). One SVG per note type: whole, half, quarter, eighth, sixteenth note.
- **D-04:** Left column layout: SVG note symbol + note name as a heading (e.g. "Whole Note" above/below the glyph). Audience is notation-naive — the name helps them connect term to symbol.

### Slide 9 — Notation Diagram (Phase 1 version)
- **D-05:** Right column (text side of image-right layout): color-coded bullet list showing staff positions:
  - Hi-hat (yellow) — top of staff (x noteheads above the lines)
  - Snare (blue) — middle (3rd space)
  - Kick (red) — bottom (below the staff)
- **D-06:** Image panel: scaffold the image-right slot with a placeholder; user will supply a drum sheet screenshot. Phase 2 replaces the entire slide with DrumStaff.vue.

### Slide 11 — Volunteer Role Cards
- **D-07:** Each v-click reveal renders a styled color block card (not plain text):
  - Kick card: red background (`#ef4444`) — "Chief Kick Officer — BOOM — Beats 1 and 3"
  - Snare card: blue background (`#3b82f6`) — "Snare Department Head — PAH — Beats 2 and 4"
  - Hi-hat card: yellow background (`#eab308`) — "Hi-Hat Technician — tss — Every eighth note"
  - Text on each card: white, weight 700

### Slide 12 — Groove Grid
- **D-08:** Show only Groove 1 (basic rock pattern). No second grid for Groove 2 — keeps slide focused and NOTE-04 requirements are fully satisfied with one grid.

### Act 3 Transition
- **D-09:** Fold the transition line ("Rhythm is the skeleton of music. Now let's prove it.") into slide 10 (notation quote slide) as a final line or speaker note transition cue. No separate 14th slide.

### Slide 13 — Outro
- **D-10:** Pure text: "Thanks for being my drum kit." No QR code. Clean ending for a friends PowerPoint night.

### Slide 1 — Cover
- **D-11:** Title + subline only. No presenter name/credit. Keeps it clean and impersonal — identity comes from the presentation itself.

### Speaker Notes
- **D-12:** Full spoken script on every slide: exact words to say plus timing cues. Format: `<!-- note -->` Slidev comment blocks. More effort but eliminates presenter dead air and supports practice runs.

### Claude's Discretion
- SVG artistry and exact sizing/proportions of the hand-crafted note symbols
- Whether to use `viewBox` scaling or fixed px dimensions for SVG files
- Exact wording of the full spoken script (tone: informal, funny, drummer-voice)
- Groove grid exact beat count representation (8 cells vs. 16 cells per measure for Groove 1)
- Placeholder backing track implementation (silence stub vs. short generated tone)

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Slide Content & Structure
- `idea.md` — Full brief: all 13 slide descriptions, pizza analogy copy, volunteer role text, groove patterns, timing targets per act
- `.planning/REQUIREMENTS.md` — Requirement IDs (SLIDE-01 through DEPLOY-02) and acceptance criteria
- `.planning/ROADMAP.md` Phase 1 section — Success criteria, research notes on base path and lifecycle hooks

### UI Design Contract
- `.planning/01-slide-skeleton-backing-track-deploy/01-UI-SPEC.md` — **MUST READ** — locks all layouts, typography, colors, spacing, LoopPlayer interaction contract, groove grid structure, all copy strings, presenter view contract

### Stack Constraints
- `CLAUDE.md` — Base path constraint, audio path pattern, VexFlow/Tone.js lifecycle rules, color coding constants

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- None yet — codebase is empty. Phase 1 creates the scaffold from scratch.

### Established Patterns
- No prior code patterns. Phase 1 establishes the project conventions.

### Integration Points
- `LoopPlayer.vue` → imported inline in `slides.md` on slide 12 via `<LoopPlayer />`
- SVG note symbols → referenced as `<img src="...">` or inline `<svg>` inside slide Markdown two-cols layouts
- Groove grid → inline HTML `<table>` inside slides.md slide 12 (no separate component needed)
- GitHub Pages → `base: /drum-theory/` in slides.md frontmatter, deploy via `gh-pages` npm package (manual, v2 will add GitHub Actions)

</code_context>

<specifics>
## Specific Ideas

- The Act 1 clapping/demo moments are the biggest timing risk — speaker notes should flag the "skip sixteenth note if running long" escape hatch explicitly
- LoopPlayer.vue from idea.md uses plain HTML5 `<audio loop>` — no Tone.js needed in Phase 1 (UI-SPEC confirmed this)
- SVG note symbols: drummer aesthetic, not academic — bold, slightly stylized is fine over strict SMuFL precision
- Groove 1 basic rock pattern is: HH every eighth, SN on 2 and 4, KK on 1 and 3 (from idea.md)

</specifics>

<deferred>
## Deferred Ideas

- Groove 2 on slide 12 — could be added as a second grid; noted for possible Phase 1 polish or Phase 2
- QR code on outro slide — optional enhancement; simple to add if presenter wants a link
- Presenter name/credit on cover — easy to add; not needed for friends PowerPoint night
- GitHub Actions auto-deploy (DEPLOY-03) — v2 requirement, out of Phase 1 scope

</deferred>

---

*Phase: 01-slide-skeleton-backing-track-deploy*
*Context gathered: 2026-04-19*
