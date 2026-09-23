# Floor Plan Designer

A small local tool for planning a flat. Load a floor plan image, tell it how long one
known distance is, then place furniture at real-world dimensions to see what fits.

Everything lives in [`index.html`](index.html) — one file, no build step, no
dependencies, no server. **Double-click it** and it opens in your browser.

## How to use it

1. **Load a floor plan** — the button, or drag a PNG onto the page (JPEG and WebP
   work too). It is downscaled to 2400 px on the long edge so it still fits in
   browser storage.
2. **Set the scale** — click the two ends of a distance you know, then type that
   distance in meters. Everything else is derived from it.
3. **Place furniture** — click an entry in the left catalog, then drag it into place.
   **+ New furniture…** adds your own, at any decimal size.
4. **Adjust** — the right panel edits the selected item: name, width, depth,
   rotation, shape, color, plus duplicate, z-order and delete.
5. **Measure** — click two points to get the distance between them. Stays armed for
   several in a row; <kbd>esc</kbd> stops, **Clear** removes them all.

Sizes are stored in **meters**, so re-setting the scale rescales everything correctly.

### Shortcuts

| | |
|---|---|
| drag | move (snaps to 5 cm) |
| <kbd>⌥</kbd> while dragging / rotating | ignore snapping |
| arrow keys | nudge 1 cm (<kbd>⇧</kbd> 10 cm) |
| rotate handle | drag the circle above the item — snaps to 15° |
| <kbd>⌘</kbd>/<kbd>Ctrl</kbd>+<kbd>D</kbd> | duplicate |
| <kbd>⌫</kbd> | delete |
| <kbd>esc</kbd> | deselect, or cancel calibration / measuring |

## Saving

Auto-saves to browser local storage on every change, image included, so a reload
picks up where you left off. Loading a *different* plan resets the scale, furniture
and measurements (your custom catalog survives).

**Export…** writes a self-contained `floorplan.json` with the image embedded;
**Import…** restores it. If a plan is too large for the storage quota it says so and
asks you to export, rather than silently losing work.

## Hosting it

`.github/workflows/deploy-pages.yml` publishes to GitHub Pages on every push to
`main`. Needs **Settings → Pages → Source: GitHub Actions** set once, in the web UI.

Browser storage is per-origin, so a plan saved locally will not appear on the hosted
page, and vice versa — use Export/Import to move between them. Plans never leave the
browser either way.

## Getting accurate results

- **Calibrate on the longest distance you know.** Click error is a couple of screen
  pixels either way, so a 15 m reference beats a 1 m one. A printed dimension chain
  or scale bar is ideal.
- **Sanity-check once:** set a Box to a known room's width and confirm it spans wall
  to wall. If it does, every other measurement is right too.
- **Don't use an AI-"cleaned" floor plan.** Image models redraw rather than copy —
  in testing, a regenerated plan matched the real drawing to 1–3 cm in most places
  but was off by up to **20 cm** in others. Calibrating fixes the overall scale; it
  cannot fix a wall that is in the wrong place.

## Deliberately not included

No undo/redo, and no pan/zoom — so **deleting is permanent**, and the plan is always
fitted to the window (the two side panels take ~460 px, so give it a wide window).
Single plan at a time.

## Implementation note

The SVG `viewBox` is the image's pixel size, so all geometry is stored in image
pixels and is independent of window size, while sizes are stored in meters. Furniture
is real SVG elements, so selecting, dragging and recoloring are just attribute
writes. A `--k` CSS variable carries the current screen-px-per-image-px ratio so
labels and handles keep a constant on-screen size.
