---
quick_id: 260419-abc
status: complete
---

# Summary: Roles Horizontal Rectangles

Changed slide 13 ("Your Roles Tonight") from a vertical list to 3 equal-width rectangles in a horizontal flex row.

## Changes

- `slides.md`: Wrapped the 3 role cards in `display: flex; gap: 16px` container
- Each card uses `flex: 1` for equal distribution
- Each card interior uses column flex: role name → sound cue (larger) → beat description
- `v-click` reveals still work — each card appears individually on click
