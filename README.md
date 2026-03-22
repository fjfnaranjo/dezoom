# wlzoom

A comprehensive set of tools to magnify areas in a display. Mainly
intended as an accessibility tool for central vision loss and other
vision impairment conditions.

## Scope

wlzoom supports three kinds of zooms:

* Panoramic zoom: zooms-in the entire screen and allows panning across
the original content.
* Scope zoom: zooms-in a region of the screen, taking the space around
it and following the cursor.
* Regional zoom: zooms specific regions of the screen, magnifying them
by taking the space around them.

Scope zoom can be made "sticky" to fix its position, allowing cursor
movement without moving the scoped area.

Regions can be expanded automatically when the cursor enters the
original or zoomed area.

## Data model

wlzoom supports dynamic alterations of the properties of this zooms. A
protocol will support modifications of this properties to allow other
clients to implement vision related accessibility functionality.

Reference of the internal data structure:

```c
enum wlzoomshape {
  WLZ_SQUARE,
  WLZ_CIRCLE,
};

enum wlzoomarea {
  unsigned float scale = 1.5;
  wlzoomshape shape = WLZ_SQUARE;
  float x = 0.0; // original square top-left position
  float y = 0.0;
  float w = 0.0; // original square size
  float h = 0.0;
  float dx = 0.0; // destination square top-left position
  float dy = 0.0;
};

// WLZ_CIRCLE
// x, y    original circle center position from screen top-left
// h, w    radius in pixels, as total% ex. 270 pixels for 1080p
// dx, dy  destination circle center position from screen top-left

struct wlzoom {
  float pan_scale = 1.0;
  float pan_x = 0.0; // zoomed view top-left corner position
  float pan_y = 0.0;

  bool scope_enabled = false;
  bool scope_stuck = false;
  wlzoomarea scope_area;

  wlzoomregion *regions;
};

enum wlzoomexpand {
  WLZ_NONE,
  WLZ_ORIGIN,
  WLZ_DEST,
};

struct wlzoomregion {
  bool enabled = false;
  wlzoomexpand expand = WLZ_ORIGIN;
  wlzoomarea area;
}
```

## Reference implementation

### labwc

Ideal candidate because already has a magnifier tool that supports both
panoramic and scope zooms.

Proposed implementation path:

- Data model:
  - Add data model.
  - Persist xml configuration in new data model on boot.
  - Replace existing data model for the current magnifier.
- Expand xml configuration to allow region configuration.
- Add regional zoom support.

## Wayland control protocol

Waiting for reference implementations and user reports to validate data
model.

## Origins and acknowledgments

This design merges the features of various applications I was using
before moving to Wayland. They worked fine in isolation but the feature
sets were needed for complex interfaces (videogames and dense UIs like
Godot).

Panoramic zoom: MacOS zoom and
[boomer](https://github.com/tsoding/boomer).

Scope zoom: [xzoom](https://tracker.debian.org/pkg/xzoom) (or
[xzoom ftp](ftp://sunsite.unc.edu/pub/linux/libs/X/), and later
[kmag](https://apps.kde.org/en-gb/kmag/).
