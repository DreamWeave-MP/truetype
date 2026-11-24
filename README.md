# truetype

TrueType fonts read in JSL, for [Jess](https://github.com/DreamWeave-MP/jess): names, vertical
metrics, character maps, kerning, glyph outlines, and variable fonts' axes. The package is
`org.dreamweave.truetype`; its one module is `@truetype`.

```luau
--!strict
local truetype = require '@truetype'

local font = truetype.load '/usr/share/fonts/google-noto/NotoSans-Regular.ttf'
local metrics = font:measure('Hello', 48)

print(font.family, font.subfamily, metrics.width)
```

It reads; it doesn't draw. `Font:outline(pen, text, size, x, y, options)` hands each glyph's
contours, scaled to the size and placed from an origin on the baseline (y down), to a pen: a
table of `moveTo`, `lineTo`, `quadTo` and `close`. Anything that fills shapes can be one; the
Magick package's `Path:pen()` is.

```luau
--!strict
local magick = require '@magick'
local path = require '@magick/path'
local truetype = require '@truetype'

local font = truetype.load '/usr/share/fonts/google-noto/NotoSans-Bold.ttf'
local image = magick.new(640, 160, '#070707')
local shape = path.new()

font:outline(shape:pen(), 'Hello', 96, 24, 120, { tracking = 2 })
shape:fill(image, '#ffe6ff')
magick.writePNG('hello.png', image)
```

## Variable fonts

A variable font loads at its default position. `font.axes` lists its axes from `fvar` (`tag`,
`name`, `minimum`, `default`, `maximum`, and `hidden` for one meant for programs rather than
people), `font.instances` its named instances (`name`, and `coordinates` by axis tag), and
`font.position` where the font is, in user coordinates by tag.

`Font:vary(coordinates)` gives the font at another position, in user coordinates by axis tag. An
axis it doesn't name keeps the font's position, a value past an axis's range is clamped to it,
and a tag the font has no axis for is an error. What comes back is a `Font` like any other,
sharing the first one's bytes: `outline`, `measure` and `advance` all answer at its position. Each
position is kept with the outlines read at it, so varying to the same place again (every frame,
say) costs nothing, and at every axis's default `vary` returns the font as loaded.

```luau
--!strict
local truetype = require '@truetype'

local font = truetype.load '/usr/share/fonts/google-noto-vf/NotoSans[wght].ttf'
local bold = font:vary { wght = 700 }

for _, instance in font.instances do
  print(instance.name, font:vary(instance.coordinates):measure('Hello', 48).width)
end

print(bold:measure('Hello', 48).width, bold.position.wght)
```

Coordinates are normalized as the OpenType variations overview says, through `avar` when the
font has one. Outlines move by `gvar`'s deltas, with the points a region leaves out inferred from
their neighbours, and a composite's components move by theirs. Advances move by `gvar`'s phantom
points, not `HVAR`, so a run's width and its outlines always agree; between named instances an
advance may be a fraction of a unit.

## What it reads

- `.ttf` and `.otf` files with TrueType (`glyf`) outlines: simple glyphs and composites, moved,
  scaled and transformed by their components' matrices.
- Character maps of format 4 and format 12, Unicode first.
- Kerning: GPOS pair adjustments (formats 1 and 2, extension lookups included) from the `kern`
  feature of the Latin script or else the default one, or a `kern` table's format 0 pairs when
  the font has no GPOS kerning. A pair's adjustment is its first glyph's x advance.
- Names (family, subfamily, full name, version, preferring the typographic family), the em, the
  ascender, descender and line gap, and `OS/2`'s cap height and x height.
- Variable fonts with TrueType outlines: `fvar`'s axes and named instances, `avar`'s segment
  maps, and `gvar`'s glyph variations (shared and embedded peaks, intermediate regions, shared
  and private point numbers, inferred deltas, composites, and phantom points for advances).

## What it doesn't

No hinting, no glyph substitution (ligatures, contextual forms, complex scripts), no CFF or CFF2
outlines, no collections, WOFF or WOFF2. A run is one glyph a character, left to right.

A variable font's kerning and vertical metrics stay its default instance's: GPOS device deltas,
`HVAR`, `VVAR` and `MVAR` aren't read, and neither are `avar` version 2's extra mappings.

## Capabilities

`load` reads the file when given a path, which needs `filesystem.read`; given the bytes, it needs
nothing.

## License

MIT OR Apache-2.0.
