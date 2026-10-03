# JP field ambient writer, 2026-10-04 JST

Original-ROM emulator observation, not a physical DS measurement.

The exact F06M0400 model header remains unchanged across the observed material1 word transition. Source copy writes2529FFFF. Native writer020B8BCC inside020B8B58 later writes41E5FFFF. The captured register input is41E5, and replay `(old & 0x8000FFFF) | (ambient << 16)` exactly produces the observed word. Captured96 code bytes match original SDK bytes.

Setter entry LR020B8DA4 confirms all-material loop020B8D78. Source dispatcher02053808 obtains environment+40 and jumps to that loop. At captured entry, environment02107874+40 is41E5. Upstream time/event selection is unresolved and this does not establish a universal constant.

With captured RAM material values explicitly supplied,1490source vertices match native clip-position and UV.1487 agree with every duplicate native key;1489 agree with at least one native color. One nonmatching key remains (F06M1300 shape2), and5463source vertices lack matching keys. Three models lack unique runtime headers; one texture-transform subset is unsupported. This is conditional lighting validation, not all-map, raster or monster-identification acceptance.

Earlier ROM-only baseline0/1490and failed writer watches are retained privately. Watchv1/v2 watched only the middle byte address and missed the32-bit store; v3 covered all four start addresses. v4 entered execution tracing too early and exhausted attempted steps before map transition; v5/v6 delay entry tracing until after transition. None of these stopping observations proves AT timing.

No ROM, RAM, State, extracted models, textures or video evidence is included in this repository.
