+++
title = "truetype"
description = "TrueType fonts read in JSL for Jess: names, metrics, character maps, kerning and glyph outlines."
date = 2026-10-21

[taxonomies]
tags = ["Jess", "Luau"]

[extra]
sections = ["overview", "releases", "credits"]
+++

TrueType fonts read in JSL for Jess: names, metrics, character maps, kerning and glyph outlines.

<!-- more -->

A [Jess](https://github.com/DreamWeave-MP/jess) package, `org.dreamweave.truetype`. Add it to a `jess.toml` and
`jess workspace sync` pins it from this site:

```toml
[dependencies]
"org.dreamweave.truetype" = "0.1"
```

It reads a font and lays out a run of text; it draws nothing itself. A glyph's outline goes to a
pen, four plain functions (`moveTo`, `lineTo`, `quadTo`, `close`), so whatever fills shapes can
take it: [Magick](https://dreamweave-mp.github.io/magick/)'s `Path:pen()` is one.

Variable fonts with TrueType outlines vary. `font.axes` and `font.instances` are what `fvar`
declares, and `font:vary { wght = 700 }` is the font at that position, a font like any other:
coordinates normalized through `avar`, outlines moved by `gvar`, and advances by its phantom
points, so a run measures as it draws. Each position is kept once read, so drawing at it again is
cheap. Kerning and vertical metrics stay the default instance's.

Every release below is the package's own archive, with its content digest beside the archive's
SHA-256; `jess.lock` pins the first and checks both.
