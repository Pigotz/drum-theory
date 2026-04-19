# Phase 1: Slide Skeleton + Backing Track + Deploy - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-04-19
**Phase:** 01-slide-skeleton-backing-track-deploy
**Mode:** discuss
**Areas discussed:** Asset sourcing, Note symbols, Slide 9 placeholder, Speaker notes, Groove 2, Volunteer card styling, Note name on slides 3–7, Act 3 transition, Outro QR code, Scaffold approach, Cover extras, Slide 9 image source

---

## Asset Sourcing

| Option | Description | Selected |
|--------|-------------|----------|
| I have both | Phase 1 assumes both files exist in public/ | |
| I have neither | Phase 1 includes stub/placeholder tasks | ✓ |
| I have the backing track, not the image | Phase 1 stubs the drum sheet image only | |
| I have the image, not the backing track | Phase 1 stubs the backing track only | |

**User's choice:** I have neither  
**Notes:** Phase 1 will include placeholder stubs for both backing.mp3 and drum sheet images.

---

## Note Symbols (Slides 3–7)

| Option | Description | Selected |
|--------|-------------|----------|
| Unicode music symbols | ♩ ♪ ♫ inline in slide Markdown | |
| Styled text labels | WHOLE, HALF, QUARTER in display typography | |
| SVG/image files | Proper music engraving quality | ✓ |

**User's choice:** SVG/image files  
**Follow-up — source:**

| Option | Description | Selected |
|--------|-------------|----------|
| I'll provide the SVG files | Phase 1 stubs placeholder slots | |
| Use a music SVG font (Bravura) | Install SMuFL-compliant web font | |
| Generate via VexFlow (early) | Blurs Phase 1/2 boundary | |
| Draw from scratch | Claude draws all 5 SVG note symbols | ✓ |

**User's choice:** Draw from scratch (Claude authors all 5 SVG note symbols)

---

## Slide 9 Placeholder

| Option | Description | Selected |
|--------|-------------|----------|
| Color-coded bullet list | Three rows with instrument, color, staff position | ✓ |
| Placeholder image frame | Gray box with "DrumStaff coming in Phase 2" | |
| HTML mockup stave | CSS approximation of a staff with colored dots | |

**User's choice:** Color-coded bullet list (hi-hat yellow / snare blue / kick red)

---

## Speaker Notes Depth

| Option | Description | Selected |
|--------|-------------|----------|
| Full spoken script | Exact words + timing cues per slide | ✓ |
| Timing cues + key prompts | Brief notes, presenter improvises phrasing | |
| Timing cues only | Duration targets only | |

**User's choice:** Full spoken script

---

## Groove 2 on Slide 12

| Option | Description | Selected |
|--------|-------------|----------|
| Groove 1 only | One color grid, NOTE-04 satisfied | ✓ |
| Groove 1 + Groove 2 | Two grids showing the variation | |

**User's choice:** Groove 1 only

---

## Volunteer Card Styling (Slide 11)

| Option | Description | Selected |
|--------|-------------|----------|
| Styled color blocks | Red/blue/yellow card backgrounds | ✓ |
| Plain text bullets | Color in label name only | |

**User's choice:** Styled color blocks (reinforces the color coding introduced in Act 2)

---

## Note Name on Slides 3–7

| Option | Description | Selected |
|--------|-------------|----------|
| Symbol + note name heading | e.g. "Whole Note" above the SVG | ✓ |
| Symbol only | Just the SVG glyph, name from spoken explanation | |

**User's choice:** Symbol + note name heading

---

## Act 3 Transition

| Option | Description | Selected |
|--------|-------------|----------|
| Fold into slide 10 | Appended as final line on notation quote slide | ✓ |
| Own minimal slide | 14th slide, dramatic pause before Act 4 | |

**User's choice:** Fold into slide 10

---

## Outro QR Code (Slide 13)

| Option | Description | Selected |
|--------|-------------|----------|
| Pure text only | "Thanks for being my drum kit." — clean ending | ✓ |
| QR code placeholder | Slot for a link to add later | |

**User's choice:** Pure text only

---

## Scaffold Approach

| Option | Description | Selected |
|--------|-------------|----------|
| npm create slidev@latest | Official scaffold, prune demo content | ✓ |
| Manual setup | Write package.json and slides.md from scratch | |

**User's choice:** npm create slidev@latest

---

## Cover Extras (Slide 1)

| Option | Description | Selected |
|--------|-------------|----------|
| Title + subline only | Clean, no presenter name | ✓ |
| Add presenter name | Standard presentation format | |

**User's choice:** Title + subline only

---

## Slide 9 Image Source

| Option | Description | Selected |
|--------|-------------|----------|
| User provides drum sheet screenshot | Slot scaffolded, user supplies image | ✓ |
| Reuse slide 2 drum sheet image | Same image, may look repetitive | |
| Placeholder gray box | Explicit but visually incomplete | |

**User's choice:** User will provide a drum sheet screenshot — Phase 1 scaffolds the slot

---

## Claude's Discretion

- SVG artistry and exact proportions for hand-crafted note symbols
- Exact wording of full spoken scripts (tone: informal, drummer-voice)
- Groove grid cell count (8 vs. 16 cells)
- Placeholder backing track implementation
