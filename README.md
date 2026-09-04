# Floor Plan Designer

A small local tool for planning a flat. Load a floor plan image, tell it how long one
known distance is, then place furniture at real-world dimensions to see what fits.

Everything lives in [`index.html`](index.html) — one file, no build step, no
dependencies, no server. **Double-click it** and it opens in your browser.

## How to use it

1. **Load a floor plan** — the button, or drag an image onto the page.
   PNG, JPEG or WebP. It gets downscaled to 2400 px on the long edge (plenty on
   screen) so the project still fits in browser storage.
2. **Set the scale** ("Maßstab") — click the two ends of a distance you know, then
   type that distance in metres. Everything else is derived from it.
3. **Place furniture** — click an entry in the left catalog; it appears on the plan.
   Drag it where you want it.
4. **Adjust** — the right panel edits the selected item: name, width, depth,
   rotation, rectangle vs. round, and colour. Also duplicate, z-order and delete.

Furniture sizes are stored in **metres**, not pixels, so if you re-set the scale
later everything rescales itself correctly.

### Shortcuts

| | |
|---|---|
| drag | move (snaps to 5 cm) |
| <kbd>⌥</kbd> while dragging / rotating | ignore snapping |
| arrow keys | nudge 1 cm (<kbd>⇧</kbd> 10 cm) |
| rotate handle | drag the circle above the item — snaps to 15° |
| <kbd>⌘</kbd>/<kbd>Ctrl</kbd>+<kbd>D</kbd> | duplicate |
| <kbd>⌫</kbd> | delete |
| <kbd>esc</kbd> | deselect, or cancel calibration |

### Your own furniture

**+ New furniture…** at the bottom of the catalog takes a name, width, depth, shape
and colour. It's added to the catalog and kept there. Any decimal size is accepted —
you are not limited to the 5 cm placement grid.

## Saving

- **Auto-saves** to browser local storage on every change, including the plan image,
  so a reload picks up where you left off. Loading a *different* plan resets the scale
  and the placed furniture (your custom catalog survives).
- **Export…** writes `floorplan.json` with the image embedded, so it is
  self-contained — keep it as a backup or move it to another machine.
  **Import…** restores it.
- If a plan is ever too large for the storage quota, it says so and asks you to
  export instead of silently losing work.

## Starting from a PDF

The tool reads images, not PDFs. Convert first:

```bash
pdftoppm -png -r 300 Grundriss.pdf plan
```

That writes `plan-1.png`. Raise `-r` for more detail, and crop to the plan itself if
the page is mostly margin — resolution spent on white space is wasted.

## Getting accurate results

- **Calibrate on the longest distance you know.** Click error is a couple of screen
  pixels either way, so a 15 m reference is far more accurate than a 1 m one. A
  printed dimension chain or scale bar is ideal.
- **Sanity-check once:** set a Box to a known room's width and confirm it spans
  wall to wall. If it does, every other measurement is right too.
- **Don't use an AI-"cleaned" floor plan.** Image models redraw rather than copy.
  A regenerated version of the plan in this repo's history matched the real vector
  drawing to 1–3 cm in most places but was off by up to **20 cm** in others, and
  repeated attempts disagreed with each other. Calibrating fixes the overall scale;
  it cannot fix a wall that is in the wrong place. Rasterize the original instead.

## Deliberately not included

No undo/redo, and no pan/zoom — so **deleting is permanent**, and the plan is always
fitted to the window (give the browser a reasonably wide window; the two side panels
take ~460 px). Single plan at a time.

## Implementation note

The SVG `viewBox` is set to the image's pixel size, so all geometry is stored in
image pixels and is independent of window size, while sizes are stored in metres.
Furniture is real SVG elements, so selecting, dragging and recolouring are just
attribute writes — no hit-testing or matrix maths. A `--k` CSS variable carries the
current screen-pixels-per-image-pixel ratio so labels and handles keep a constant
on-screen size at any window size.
