---
quick_id: 260419-abc
slug: roles-horizontal-rectangles
description: Slide 13 should have the 3 roles as 3 rectangles horizontally distributed
date: 2026-04-19
---

# Quick Task 260419-abc: Roles Horizontal Rectangles

## Task

Change slide 13 ("Your Roles Tonight") to display the 3 role cards as equal-width rectangles in a horizontal row instead of a vertical stack.

## Tasks

### Task 1: Update slide 13 layout

**File:** `slides.md` (lines 316–334)
**Action:** Wrap the 3 `v-click` role divs in a `display: flex; gap: 16px` container. Each role div gets `flex: 1` so they share width equally. Each card gets a column-flex interior layout (title, sound, beat info stacked inside the card).
**Done:** ✓
