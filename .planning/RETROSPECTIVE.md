# Project Retrospective

---

## Milestone: v1.0 — "You Can't Read This" MVP

**Shipped:** 2026-04-19
**Phases:** 2 | **Plans:** 7

### What Was Built

- Slidev scaffold with correct base path + GitHub Pages deploy pipeline
- 5 SVG note symbols (whole → sixteenth) + LoopPlayer.vue backing track component
- All 13 slides with v-click reveals, groove grid, and speaker notes
- VexFlow 5.0.0 percussion spike validating x-notehead API
- DrumStaff.vue — two-voice percussion stave with per-notehead color coding; slide 9 live

### What Worked

- **Spike-first approach for VexFlow** — running a throwaway `spike-vexflow.html` before writing the component API eliminated rework. v5 x-notehead syntax (`'a/5/x'`) and `setKeyStyle` coloring would have been discovered late otherwise.
- **Deploy base path first** — wiring `base: /drum-theory/` in Phase 1 plan 1 caught the highest-risk integration immediately. Paid for itself in prevented debugging later.
- **Yolo + coarse granularity** — 7 plans for a full presentation + notation component is the right granularity. More plans would have been overhead without benefit.

### What Was Inefficient

- **REQUIREMENTS.md checkbox drift** — NOTE-01/02/03 and DEPLOY-02 were completed in Phase 2 but their checkboxes were never ticked. Required manual correction at milestone close. Traceability updates should happen at plan completion, not milestone close.
- **SVG note viewBox hardcoding** — white stroke/fill was hardcoded for an assumed dark theme that turned out to be light. The seriph theme assumption wasn't verified before asset creation.

### Patterns Established

- VexFlow init always inside `onMounted` + `onSlideEnter`, guard with `innerHTML = ''` before re-render
- `import.meta.env.BASE_URL` for all asset paths — never bare absolute paths
- `await Tone.start()` as first line of every audio click handler
- Drum color coding: kick = `#ef4444`, snare = `#3b82f6`, hi-hat = `#eab308`

### Key Lessons

- Verify theme background color before writing SVG fill/stroke values
- Mark requirements complete at the moment of completion, not retrospectively
- Percussion spike is mandatory for any VexFlow work — the v5 API diverges significantly from v3/v4 tutorials

### Cost Observations

- Sessions: 1 (single day)
- Notable: Entire v1.0 shipped in one session — small, focused scope with a clear spike strategy

---

## Cross-Milestone Trends

| Milestone | Phases | Plans | Timeline | Rework? |
|-----------|--------|-------|----------|---------|
| v1.0 MVP  | 2      | 7     | 1 day    | Minor (theme color assumption) |
