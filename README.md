# truetype

TrueType fonts read in JSL, for [Jess](https://github.com/DreamWeave-MP/jess): names, vertical
metrics, character maps, kerning, and glyph outlines. The package is `org.dreamweave.truetype`;
its one module is `@truetype`.

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

## What it reads

- `.ttf` and `.otf` files with TrueType (`glyf`) outlines: simple glyphs and composites, moved,
  scaled and transformed by their components' matrices.
- Character maps of format 4 and format 12, Unicode first.
- Kerning: GPOS pair adjustments (formats 1 and 2, extension lookups included) from the `kern`
  feature of the Latin script or else the default one, or a `kern` table's format 0 pairs when
  the font has no GPOS kerning. A pair's adjustment is its first glyph's x advance.
- Names (family, subfamily, full name, version, preferring the typographic family), the em, the
  ascender, descender and line gap, and `OS/2`'s cap height and x height.

## What it doesn't

No hinting, no glyph substitution (ligatures, contextual forms, complex scripts), no CFF or CFF2
outlines, no collections, WOFF or WOFF2, and no variable-font axes. A run is one glyph a
character, left to right.

## Capabilities

`load` reads the file when given a path, which needs `filesystem.read`; given the bytes, it needs
nothing.

## License

MIT OR Apache-2.0.
