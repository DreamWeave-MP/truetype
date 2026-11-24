# Fonts

Real fonts the suite reads, beside the ones `tests/fixture.jsl` builds byte by byte. Each is under
the licence in the `OFL.txt` beside it.

| Folder | Fonts | From |
| --- | --- | --- |
| `noto-sans-cherokee/` | Noto Sans Cherokee: the variable font (`wght` 100 to 900, with `avar`) and its Thin, Regular, Bold and Black static instances | Google Fonts; the Noto Project, https://github.com/notofonts/cherokee |

These are reduced copies, made by `reduce.jsl` with the characters `Ꭰ Ꭵ ꭲ Á . …` and a space:
the glyphs those map to, and their components, keep their outlines and their glyph variations;
every other glyph is empty but keeps its number, its advance and its character map entry. Only the
tables `@truetype` reads are kept (no layout, hinting or `post` names), so they are not fonts to
set text in. The SIL Open Font License allows modified copies; Noto reserves no font names.
