---
quick_id: 260419-ghi
status: complete
---

# Summary: GrooveTable Component

Created `components/GrooveTable.vue` — accepts `groove` (hh/sn/kk arrays of 0/1)
and `beats` (label array) props with sensible defaults matching the original rock groove.

Replaced ~40 lines of hardcoded HTML in slide 14 with:

```
<GrooveTable :groove="{ hh: [...], sn: [...], kk: [...] }" />
```

To reuse for another groove, add a new slide and pass a different pattern:

```
<GrooveTable :groove="{ hh: [1,0,1,0,1,0,1,0], sn: [0,0,1,0,0,0,1,0], kk: [1,0,0,0,0,0,1,0] }" />
```
