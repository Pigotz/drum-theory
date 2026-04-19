---
status: complete
---

# Quick Task 260419-mno: Upbeat Pop slide from drum-sheet.png

## What was done

- Added slide 20 after the Disco groove (slide 19), before the end slide
- Layout: `layout: default` with flex column — title "Upbeat Pop" + GrooveTable + GrooveDrumStaff, centered, max-width 640px
- No right column, no LoopPlayer — table and VexFlow notation only
- Groove decoded from drum-sheet.png ("Upbeat Pop ♩=160"):
  - hh: [1,1,1,1,1,1,1,1] — eighth hi-hat throughout
  - sn: [0,0,1,0,0,0,1,0] — snare on beats 2 and 4
  - kk: [1,0,0,0,0,1,0,0] — kick on beat 1 and 3-and (the upbeat syncopation)
- GrooveDrumStaff width set to 640px to fill the wider centered column

## Commit

38251cf
