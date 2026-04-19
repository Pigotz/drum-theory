# Drum Theory Presentation

## Project

Slidev presentation "You Can't Read This" — a 12-minute PowerPoint night talk teaching drum notation through pizza analogies and a live Human Drum session.

See `.planning/PROJECT.md` for full context.

## Stack

- Slidev `52.14.2` + Vue 3
- VexFlow `5.0.0` (SVG backend only — not Canvas)
- Tone.js `15.1.22`
- GitHub Pages (manual deploy)

## Key Constraints

- `base: /drum-theory/` must be set in `slides.md` frontmatter
- Runtime audio paths: use `import.meta.env.BASE_URL + 'samples/kick.wav'` — not bare absolute paths
- VexFlow: all initialization inside `onMounted()`, guard null refs
- Tone.js: `await Tone.start()` must be first line of every audio click handler
- Slidev lifecycle: use `onSlideEnter`/`onSlideLeave` from `@slidev/client` — not `onMounted`/`onUnmounted`
- Color coding (consistent throughout): kick = red (`#ef4444`), snare = blue (`#3b82f6`), hi-hat = yellow (`#eab308`)

## Design References

Visual inspiration for slide aesthetics and component styling:

- https://dribbble.com/shots/11065516-Guy-drummer-and-drum-set
- https://dribbble.com/shots/4378208-Drum-till-Death
- https://dribbble.com/shots/26947901-Drum-musician-portrait

## GSD Workflow

This project uses GSD for phased planning and execution.

- Current roadmap: `.planning/ROADMAP.md`
- Project state: `.planning/STATE.md`
- Requirements: `.planning/REQUIREMENTS.md`

**Phase 1:** Slide Skeleton + Backing Track + Deploy
**Phase 2:** VexFlow Drum Notation (DrumStaff.vue)

Run `/gsd-discuss-phase 1` to start Phase 1.

Before any phase, run the relevant `/gsd-plan-phase N` to get a detailed task plan.
