# Phase 3: Visual Redesign - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-04-19
**Phase:** 03-visual-redesign
**Areas discussed:** Theme removal, CSS approach, Layout background coverage, Font availability, Drum-sheet.png, Two-cols right panel, Heading colors, Seriph heading rules, Groove grid cells, Presenter view, VexFlow SVG text, LoopPlayer navigation, Layout end/statement

---

## Theme removal vs override

| Option | Description | Selected |
|--------|-------------|----------|
| Remove seriph entirely | Delete theme: seriph from frontmatter. Clean Slidev default baseline. | ✓ |
| Keep seriph + override CSS | Keep the theme, write targeted overrides. Risks specificity battles. | |
| Replace with a dark theme | Swap for a community dark theme package. | |

**User's choice:** Remove seriph entirely  
**Notes:** Cleaner baseline, avoids fighting seriph's opinionated styles.

---

## CSS approach

| Option | Description | Selected |
|--------|-------------|----------|
| CSS custom properties | Override --slidev-theme-* variables in :root {}. Clean and cascades naturally. | ✓ |
| .slidev-layout selectors | Target layout-specific classes. More surgical. | |
| !important on key rules | Quick but brittle. Maintenance risk. | |

**User's choice:** CSS custom properties (Recommended)

---

## Layout background coverage

| Option | Description | Selected |
|--------|-------------|----------|
| body + .slidev-slide background | One rule covers all 13 slides globally. | ✓ |
| Per-layout CSS | 6 rules, more precise. | |
| Slidev headmatter style block | Per-slide, too verbose. | |

**User's choice:** body + .slidev-slide background (Recommended)

---

## Font availability offline

| Option | Description | Selected |
|--------|-------------|----------|
| Accept the risk | Google Fonts import. Presenter pre-caches at home. | ✓ |
| Self-host the font | Download woff2 files into public/fonts/. Zero offline risk. | |
| System font fallback only | No import. OS default font. | |

**User's choice:** Accept the risk (Recommended)

---

## Presenter view appearance

| Option | Description | Selected |
|--------|-------------|----------|
| Keep presenter view light | Light view easier to read notes in dim venue. | ✓ |
| Dark presenter view too | Consistent but harder to read notes in low light. | |

**User's choice:** Keep presenter view light (Recommended)

---

## layout: end and statement

| Option | Description | Selected |
|--------|-------------|----------|
| Dark bg + font only, no extras | Global changes handle both layouts. | ✓ |
| Add explicit layout overrides | Targeted CSS for .slidev-layout.end and .statement. | |

**User's choice:** Dark bg + font only, no extras

---

## drum-sheet.png (slide 2)

| Option | Description | Selected |
|--------|-------------|----------|
| CSS mix-blend-mode: multiply | White areas become transparent. No image changes needed. | ✓ |
| Regenerate with transparent PNG | Clean result but requires image editing. | |
| Accept the white rectangle | Could look intentional. | |

**User's choice:** CSS mix-blend-mode: multiply

---

## Heading color inheritance

| Option | Description | Selected |
|--------|-------------|----------|
| Global h2 rule in style.css | One rule covers all slides. No changes to slides.md. | ✓ |
| Add color inline to each h2 | Explicit but verbose — 5 slides to edit. | |

**User's choice:** Global h2 rule in style.css (Recommended)

---

## Seriph heading rule

| Option | Description | Selected |
|--------|-------------|----------|
| No replacement | Clean headings without decoration. | ✓ |
| Add a subtle rule | Custom underline using instrument color or muted dark tone. | |

**User's choice:** No replacement (Recommended)

---

## Groove grid empty cells

| Option | Description | Selected |
|--------|-------------|----------|
| CSS rule in style.css | table td {} covers all cells. No slides.md changes. | ✓ |
| Inline style on each empty cell | ~12 cells to edit. Verbose. | |

**User's choice:** CSS rule in style.css (Recommended)

---

## Two-cols right panel background

| Option | Description | Selected |
|--------|-------------|----------|
| CSS .slidev-layout.two-cols > *:last-child | Applies to all 7 two-cols slides automatically. | ✓ |
| Inline style per ::right:: block | 7 places in slides.md to edit. | |

**User's choice:** CSS selector approach (Recommended)

---

## LoopPlayer navigation state

| Option | Description | Selected |
|--------|-------------|----------|
| Fix it in this phase | Add onSlideLeave cleanup. Small change, prevents presentation bug. | ✓ |
| Defer to a later phase | Presenter manages manually. | |

**User's choice:** Fix it in this phase (Recommended)

---

## VexFlow SVG text elements

| Option | Description | Selected |
|--------|-------------|----------|
| Test it and add targeted fixes only if broken | Run dev server first. Fix only if clef/time-sig invisible. | ✓ |
| Set context style before every element draw | More defensive, adds noise to DrumStaff.vue. | |

**User's choice:** Test it and add targeted fixes only if broken

---

## Claude's Discretion

- CSS selector specificity tuning if .slidev-layout.two-cols > *:last-child doesn't match actual DOM
- Any additional VexFlow element-specific style calls discovered during testing
- Order and grouping of rules within style.css

## Deferred Ideas

None — discussion stayed within phase scope.
