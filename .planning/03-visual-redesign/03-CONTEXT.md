# Phase 3: Visual Redesign - Context

**Gathered:** 2026-04-19
**Status:** Ready for planning

<domain>
## Phase Boundary

Apply a dark visual theme across all 13 slides — dark backgrounds, Space Grotesk typography, consistent color coding, and all visual components (SVG symbols, VexFlow stave, groove grid, LoopPlayer button) render correctly and cohesively. No new features or content changes.

</domain>

<spec_lock>
## Requirements (locked via UI-SPEC.md)

**13 requirements are locked.** See `03-UI-SPEC.md` for full design contract including color tokens, typography scale, component restyle contracts, spacing, and implementation sequence.

Downstream agents MUST read `03-UI-SPEC.md` before planning or implementing.

**In scope (from UI-SPEC.md):** Dark theme tokens, Space Grotesk font, VexFlow dark-mode fix, LoopPlayer restyle, groove grid cell borders, slide-by-slide layout targets, implementation sequence
**Out of scope (from UI-SPEC.md):** New features, content/copy changes, PlayableDrumStaff.vue, audio interactivity

</spec_lock>

<decisions>
## Implementation Decisions

### Theme & Baseline

- **D-01:** Remove `theme: seriph` from frontmatter entirely — start from Slidev's minimal default. Do NOT keep seriph or replace with another community theme.
- **D-02:** All dark overrides written as CSS custom property overrides in `style.css` (`--slidev-theme-*` variables in `:root {}`). No `!important` unless strictly unavoidable.

### Background Coverage

- **D-03:** Global dark background applied via `body` + `.slidev-slide` in `style.css`. One rule covers all 13 slides and all 6 layouts. No per-layout CSS rules needed.
- **D-04:** Presenter view intentionally left light — easier to read presenter notes in a dim venue. No presenter-view CSS override.

### Typography

- **D-05:** Space Grotesk imported via Google Fonts in `style.css`. Accept offline risk — presenter can pre-load at home to cache the font.
- **D-06:** Heading color applied globally via `h2 { color: #e2e8f0 }` in `style.css`. No inline color changes to slides.md heading tags.
- **D-07:** No replacement for seriph's h1/h2 underline rule — let headings stand without decoration. Clean, minimal aesthetic.

### Layout: end and statement

- **D-08:** No extra CSS overrides for `layout: end` (slide 13) or `layout: statement` (slide 10). Global background + font changes are sufficient for both.

### Slide 2 — drum-sheet.png

- **D-09:** Apply `mix-blend-mode: multiply` to the `<img>` on slide 2 via CSS. White areas of the PNG become transparent against the dark background — no image file changes needed.

### Two-cols Right Panels

- **D-10:** Apply `#1a1d27` to the right panel via CSS selector `.slidev-layout.two-cols > *:last-child { background: #1a1d27 }`. No inline style changes to slides.md `::right::` blocks.

### Groove Grid

- **D-11:** Empty `<td>` cells styled via CSS rule `table td { background: #1a1d27; border: 1px solid #2d3244 }` in `style.css`. Colored cells (kick/snare/hi-hat) retain their inline background styles which take precedence.

### VexFlow Dark-Mode Fix

- **D-12:** Set `ctx.setStrokeStyle('#e2e8f0')` and `ctx.setFillStyle('#e2e8f0')` on the VexFlow context BEFORE calling `stave.setContext(ctx).draw()`. This is the minimal fix per UI-SPEC.
- **D-13:** Test-first approach for VexFlow text elements (clef symbol, time-sig numerals): run dev server after D-12 change, add targeted style calls only if those elements are still invisible. Do not pre-optimize.

### LoopPlayer

- **D-14:** Fix LoopPlayer lifecycle in this phase: add `onSlideLeave` hook (from `@slidev/client`) to pause audio and reset `playing` ref. Prevents audio persisting when navigating away from slide 12.

### Claude's Discretion

- CSS selector specificity tuning if `.slidev-layout.two-cols > *:last-child` doesn't match the actual DOM structure (Claude should inspect and adjust)
- Any additional VexFlow element-specific style calls discovered during D-13 test
- Order and grouping of rules within `style.css`

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Design Contract
- `.planning/03-visual-redesign/03-UI-SPEC.md` — Full design contract: color tokens, typography, component restyle contracts, spacing, implementation sequence (MUST READ)

### Requirements
- `.planning/REQUIREMENTS.md` — THEME-01/02, SVG-01/02, TYPE-01/02, COL-01/02, VEX-01/02, LAY-01/02/03
- `.planning/PROJECT.md` — Key Decisions table, design constraints, color coding lock

### Codebase Constraints
- `CLAUDE.md` — VexFlow SVG backend only, onSlideEnter/onSlideLeave lifecycle, BASE_URL audio paths, color coding hex values

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `components/DrumStaff.vue` — VexFlow stave component. Needs: `ctx.setStrokeStyle/setFillStyle('#e2e8f0')` added before `stave.setContext(ctx).draw()` (step 3 in render()). Per-note colors (step 6) are already correct and untouched.
- `components/LoopPlayer.vue` — Audio player component. Needs: button restyle (white/gray per UI-SPEC) + `onSlideLeave` cleanup to pause and reset state.
- `slides.md` — All 13 slides inline. The groove grid table (slide 12) has 20+ `<td>` elements; CSS rule handles them without touching slides.md.

### Established Patterns
- VexFlow `setStyle()` and `setKeyStyle()` for per-note colors already works correctly — coloring happens at step 6 (before voice draw), which is after the new context style set at step 3.
- `onSlideEnter` already used in DrumStaff.vue for re-render — `onSlideLeave` is the symmetric hook for LoopPlayer cleanup.
- All audio paths use `import.meta.env.BASE_URL` prefix — no changes needed for audio.

### Integration Points
- `style.css` at project root — Slidev auto-imports this file. File does not yet exist; must be created.
- Frontmatter `theme: seriph` line in `slides.md` — must be removed.
- VexFlow render function in `DrumStaff.vue` — ctx style set happens between step 3 (stave draw) setup and step 3's draw call.

</code_context>

<specifics>
## Specific Ideas

- Seriph underline rule: no replacement — clean headings only, as per the Dribbble reference aesthetic
- Groove grid: CSS `table td` rule covers all empty cells; inline styles on colored cells already use `!important` semantics via specificity (inline > class > tag)
- LoopPlayer button: exact styling from UI-SPEC — Play state: `background: #e2e8f0; color: #0f1117`, Stop state: `background: #4b5563; color: #e2e8f0`. Text: plain "Play" / "Stop", no emoji.

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

All audio/interactivity features (PlayableDrumStaff.vue, beat cursor) remain deferred to future phases per REQUIREMENTS.md.

</deferred>

---

*Phase: 03-visual-redesign*
*Context gathered: 2026-04-19*
