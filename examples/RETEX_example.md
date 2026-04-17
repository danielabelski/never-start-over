# RETEX — SVG Badge Generator

---

## 1. CONTEXT

Building a custom SVG badge generator for project READMEs. AI was tasked with producing pixel-perfect badges matching the shields.io style. 4 failed iterations before resolution.

---

## 2. ERROR CHRONOLOGY

| # | Attempt | What Was Done | Why It Failed | Bias |
|---|---------|--------------|---------------|------|
| 1 | Hardcoded SVG with manual coordinates | Text overflowed badge bounds | Precision bias — assumed pixel values would be right first try |
| 2 | Added viewBox scaling | Text scaled but became blurry | Local Patch — fixed one thing, broke another |
| 3 | Switched to foreignObject + HTML text | Broke in Safari/Firefox rendering | Novelty bias — chased "clever" solution |
| 4 | Used SVG text with `textLength` attribute | Badges render correctly cross-browser | N/A (solution) |

**Error cycle duration**: ~45 minutes  
**Final solution duration**: ~5 minutes

---

## 3. ANTI-PATTERNS

**The Fly Against the Window**  
- Definition: Repeating the same SVG manipulation approach (adjusting coordinates, scaling, padding) instead of questioning the rendering method.  
- Example: Attempts 1-2 both tried to make hardcoded SVG coords work.  
- Signal: 2nd failed attempt with coordinate tweaks → stop, change rendering strategy.

**The Scribble**  
- Definition: Hand-crafting SVG coordinates when the shields.io open-source code already solves text measurement.  
- Example: Writing `x="47" y="14"` by feel instead of looking at how shields.io calculates positions.  
- Signal: If you're guessing pixel values → find the reference implementation.

---

## 4. TIPPING POINT

- **Moment**: User asked "How does shields.io actually do it?"
- **What the AI thought**: The problem was SVG coordinate precision
- **The real problem**: The rendering method was wrong — SVG `text` with `textLength` handles variable-width fonts natively
- **Lesson**: Look at how the established tool solves it before inventing from scratch

---

## 5. BEST PRACTICES

- ALWAYS check existing implementations before building SVG from scratch
- Use SVG-native text features (`textLength`, `lengthAdjust`) before resorting to foreignObject
- Test SVG rendering in at least 2 browser engines before declaring success
- When pixel values require guessing, the approach is wrong

---

## 6. VACCINE

```
VACCINE_RETEX_012:
SVG text positioning: use textLength attribute, never hardcode coordinates for variable-width text.
Before building SVG from scratch: check how the canonical tool (shields.io, d3, etc.) solves it.
foreignObject in SVG: avoid for cross-browser content — Safari/Firefox handle it inconsistently.
2nd failed coordinate tweak on same SVG element = STOP, change rendering strategy entirely.
```

---

```yaml
uri: session-xyz-789
date: 12-03-26
project: Badge-Generator
type: RETEX
subject: SVG text positioning and cross-browser rendering
iterations: 4
error_duration: ~45 min
solution_duration: ~5 min
resolution: SVG text with textLength attribute
anti_patterns: 2 (The Fly Against the Window, The Scribble)
vaccine: VACCINE_RETEX_012
```
