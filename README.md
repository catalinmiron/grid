[GridList](http://ubervu.github.io/grid/) [![Build Status](https://travis-ci.org/uberVU/grid.svg?branch=17-travisci-integration)](https://travis-ci.org/uberVU/grid)
====
Drag and drop library for a two-dimensional resizable and responsive list of
items

**Demo: http://ubervu.github.io/grid/**

The GridList library is split into two roles:

1. An [agnostic GridList class](src/gridList.js) that manages the
two-dimensional positions from a list of items within a virtual matrix
2. A [jQuery plugin](src/jquery.gridList.js) built on top of the GridList class
that translates the generic items positions into responsive DOM elements with
drag and drop capabilities

## GridList class

```js
var gridList = new GridList(items, {rows: 3});
```

Here are the main functions of the GridList class:

- **findPositionForItem**: Generate new positions inside a 2d grid. The
positioning algorithm places items in columns, starting from left to right,
going through each column top to bottom
- **resize**: Convert item positions from one grid size to another, maintaining
as much of their order as possible
- **moveItemToPosition**: Handle collisions when moving an item over another
- **resizeItem**: Resize an item and reposition colliding items around.
**TODO:** Currently only the width of an item (w) can be resized
- **getChangedItems**: Compare a list of items with that of a GridList instance
and return the changed items only (diff). Useful when updating item sizes and
positions in a persistent medium (e.g. syncing grid to db with minimum payload)

The size of an item is expressed using the number of cols and rows it takes up
within the grid (w and h). The position of an item is expressed using the col
and row position within the grid (x and y)

An item is an object of structure:
```js
{
  w: 3, h: 1,
  x: 0, y: 1
}
```

## $.fn.gridList

```js
$('.my-list').gridList({rows: 3});
```

The jQuery plugin has two main functions:

- Render the GridList on top of a list of **DOM elements.** The list items are
expected to have `data-w` and `data-h` attributes, and optionally `data-x` and
`data-y` (if their positions have been previously generated and persisted)
- **Drag and drop** capabilities

The rendered list is **responsive** to its parent container, meaning that the
width and height of the items are calculated based on the container height
divided by the number of grid rows.

## FAQ: Why not [gridster](https://github.com/ducksboard/gridster.js)?

- Their README reads Ducksboard is no longer active in their development. There
are a few notable forks but it's hard to assert their [reliability.](https://github.com/dustmoo/gridster.js/issues)
- gridster works vertically and our design is horizontal. We instigated a
gridster pull request that attempted to make gridster work both ways and it
didn't seem to stabilize any time soon, plus the code was too complex to 
approach. Our lib ended up having less than 5 times fewer code.
- gridster collisions are [very basic](https://github.com/ducksboard/gridster.js/issues/54),
we pushed towards better UX and found alternative ways for dealing with
collisions.
- We wanted out-of-the-box responsiveness, and the entire grid system was build
fluid, relative to any parent container.
- We needed the grid logic to be a DOM-less lib outside the jQuery plugin. This
allows us to compute grid positions on the server-side and run kick-ass fast
tests with Node.
- Another more particular thing we needed was widgets that had height=0, which
means they stretch on however many rows a grid has. We show timelines like 
this. The same can be easily adapted for width in vertical grids.

*Please check [demo page](http://ubervu.github.io/grid/) or code directly for
investigating these assumptions.*
