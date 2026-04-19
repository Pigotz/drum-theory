---
quick_id: 260419-ghi
slug: groove-table-component
description: Extract slide 14 groove table into reusable GrooveTable.vue component
date: 2026-04-19
---

# Quick Task 260419-ghi: GrooveTable Component

## Tasks

### Task 1: Create components/GrooveTable.vue

Accepts `groove` (Object with hh/sn/kk arrays of 0/1) and `beats` (Array of labels) props.
Renders the colored grid table. Defaults to the standard rock groove.

### Task 2: Replace hardcoded table in slide 14

Replace ~40 lines of inline HTML with `<GrooveTable :groove="{...}" />`.
