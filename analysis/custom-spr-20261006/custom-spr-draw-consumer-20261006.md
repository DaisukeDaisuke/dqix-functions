# Loaded SPR cell draw and layout consumers

Japanese YDQJ revision0 static source closure, 2026-10-06 UTC. This supplements the structural reader and allocation/transfer proof. It does not observe the live draw, current matrices, placement bytes, color, alpha or animation phase. The structural v2 parser remains isolated and unchanged; no app consumer, sprite renderer or UI mask is activated.

## Direct ID0 route

The previously traced field table binds ID0 to wing1.spr. At0219bb68–0219bb7c the field path leaves r1=0 and sets r2=1 when calling02048374 on caller-object+2980. Thus this reached branch requests stored frame0 and texture flag1. It does not call the generic animation update in this immediate path. The preceding placement reads caller-object bytes+3424/+3436, which remain unobserved in the video. Filename/table membership alone does not identify the visible command fragment.

## Cell texture parameters, proven from register writes

02048374 selects frameRecord = object.frames + requestedFrame×8, iterates its low-byte cell count and calls02048538 for each8-byte loaded cell.

02048538 assembles and writes the texture parameter at040004a8. It combines source object type shifted26, cell texture address divided8, width-shift bits at20, height-shift bits at23 and caller flag shifted29. For the measured cells, shifts are within the three-bit extent fields and the texture address is the low16-bit allocation handle scaled8 by the loader. Consequently the ID0 branch's type3 and flag1 give format3 and color-index0 transparency, while repeat/flip/texture-transform fields remain zero. This conclusion follows from the actual SPR loader/draw connection, not a guessed NCER/NCGR layout.

The current native-tex0 and native-texture-unpack modules interpret those same parameter fields: format3 is linear4-bit palette indexing, the lower nibble first, with the color-index0 flag controlling zero alpha. Texture bytes reach the upload unchanged; no cell-specific tile shuffle appears in the loader or transfer chain. The existing unpack component was used directly on each proved cell span and palette-transfer span, with both flag alternatives retained for diagnostics. Only hashes/counts were saved. No new texture decoder, rasterizer or assembled screen sprite was introduced.

The palette-base register at040004ac receives object.paletteAddress divided16 for type3 (the draw uses division8 only for type2). The draw also reads object+80 for color and+82 for the polygon alpha field. Initialization supplies values, but later/runtime values are not inferred here. The existing component's RGB555-to6665 expansion is not a whole native-framebuffer parity claim.

## Cell-local geometry

The source loader retains each cell's unsigned X-like byte and its inverted Y-like byte. The draw translates by those packed values in20.12 units and uses the source cell extents. Its direct-frame outer transform adds frameHeight×scaleY to objectY, then negates the Y scale. This makes the serialized top-down cell coordinates compatible with the existing linear texture rows under this branch. It does not establish the caller's projection, current screen origin or live scale, so no video position is supplied from this algebra.

## Layout word meanings and a retained invalid-next alternative

The source loader interleaves the three serialized word planes into12-byte keys. Consumers close the roles:

- 020480dc reads key+0 and writes it to the object's current-key index at+70.
- 020480f0 reads key+4 and writes it to the duration field at+7c.
- 02048248 reads key+8 as the stored frame index passed to02048374.

The update uses unsigned elapsed difference from a source global tick at02114af0 and a pause byte at02114aa0. Neither its tick rate nor any current value is inferred from video PTS. The layout selector020486dc sets current-key0, the selected layout, last tick and the first duration. Named selection0204874c uses the30-byte record lookup helper02048ed8. The opaque leading30-byte file header is still not interpreted.

The four actual resources have terminated source labels in these record slots. Fuki variants contain a four-key cycle0→1→2→3→0, with frame sequence0,1,2,1; duration values are10 or30 according to the specific source member. Wing1 has two one-key records whose next field is1, outside their single-key arrays. This is preserved as unsupported for generic animation replay. The traced ID0 branch directly requests frame0; it is not evidence that all generic updater paths are safe or that the out-of-range next field should be repaired.

## Palette overlap result and remaining input

Although fuki_com's requested32-byte transfer span overlaps16 header bytes beyond its16-byte serialized palette advance, every palette index used by its stored cells is within the declared palette bytes. The same is true of the other three members. Thus these stored cells do not index the overlapping header words. Allocation, requested transfer span and actual runtime execution remain distinct, as documented separately.

The remaining blocker for a same-frame UI hypothesis is the resource-to-observed-fragment association and current draw context/placement. No command text is injected, no manual box is supplied, and no enemy is declared absent behind a possible UI overlay. The native D0 classification counterexample remains unchanged.
