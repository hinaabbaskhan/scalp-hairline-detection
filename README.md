# scalp-hairline-detection
we want to find the exact pixel row where the hair meets the forehead skin: the hairline.

## What problem are we solving?

Given a portrait photo, we want to find the **exact pixel row where the hair meets the forehead skin**: the hairline.

- Hair color varies enormously (black, blonde, grey, dyed)
- Lighting changes how skin and hair look
- Hairstyles vary: side-swept bangs, center parts, receding hairlines, widow's peaks
- The face may be tilted or partially obscured

**Our solution:** combine two MediaPipe models, one for face geometry, one for hair pixels, and extract the hairline from where they intersect.

---

## Architecture overview

```
INPUT IMAGE
    │
    ├─ Step 1: MediaPipe Face Landmarker
    │          → tells us WHERE the forehead is
    │            (brow_y, x_left, x_right)
    │
    ├─ Step 2: MediaPipe Hair Segmenter
    │          → tells us WHICH pixels are hair
    │            (binary mask: 255=hair, 0=not hair)
    │
    ├─ Step 3: Per-column lowest hair pixel
    │          → for each x column in the forehead region,
    │            find the lowest hair pixel above the brow
    │            → raw contour: one y value per column
    │
    └─ Step 4: Smooth the contour
               → remove outliers, apply moving average
               → final hairline_y (single number)
               → smoothed curve (for visualization)
```
