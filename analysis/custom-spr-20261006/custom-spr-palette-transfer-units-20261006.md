# Palette source-span units: allocation versus transfer

This supplements the frozen custom SPR loader note. It proves the requested transfer/source-read length in the source call chain; it does not observe a successful allocation, accepted queue item, DMA completion or any live-frame upload. No parser, background/body gate, renderer or HUD mask changes follow from this note.

## Independent allocation operation

02048960 selects the palette helper's size argument at02048b20–02048b6c:32 for raw count≤16,512 for count≤256, otherwise the raw count. At02048da8–02048db4, helper02048d90 passes that size to allocator020bd298. The allocator separately rounds an allocation request to8-byte units at020bd29c–020bd2ac. Its alignment operation alone would not establish a source read.

## Same size also reaches a byte-copy/transfer path

The helper saves the original size in r6, independently of the allocator's returned handle. At02048e00–02048e14 it passes type0, the source pointer, the derived destination offset, and original size in r3 to020ddae8. The two extra arguments are0 and the source-lifetime flag. The helper's optional earlier owned-copy branch also uses original r6 as the byte count at02048dec–02048df4, through the verified byte-copy helper0200195c.

Wrapper020ddae8 places its original r3 size as the fifth argument to01ff8b54. After the latter's48-byte prologue,01ff8b70 loads that argument into r7. The source pointer is retained in r8. There are two relevant source-consistent routes:

- Queued owned-copy branch:01ff8ca8 puts source r8 in r1;01ff8cac puts size r7 in r2;01ff8cb0 calls0200195c. That helper copies exactly r2 bytes one byte at a time. Queue bookkeeping later rounds r7 upward to a multiple of4 and stores its word count at01ff8d70–01ff8da4. For32, no rounding changes the length.
- Immediate dispatch:01ff8bec–01ff8bfc calls the type0 function from source table01ff8750, passing original source in r0, destination offset in r1 and size r7 in r2. The type0 table entry is020c8188. Its CPU branch calls020cbed4, which first computes destination-end = destination + byteLength, then copies words to that end. Its DMA branch reaches020cb8f8 with the same byte length;020cb958/020cb974 shift it right2 to form the DMA word count. Thus32 means32 bytes, or8 words, rather than32 palette entries or only an allocation quantum.

The queue may reject or defer the request, and the copy branch depends on source-lifetime/context state. These static paths establish units and possible read span, not their occurrence in the analyzed video.

## Exact file-span consequence

For the measured fuki_com.spr member, the count field is8. Serialized palette advance is16 bytes at offsets992–1007; the requested32-byte transfer span is992–1023. The following opaque30-byte header begins at1008. Therefore the source transfer request includes the first16 bytes of that header if the corresponding copy/transfer executes. The structural reader preserves this span instead of substituting zero padding or treating allocation size as a palette entry count.

The wing1.spr and fuki_com_apc/q.spr members have raw count16, so their32-byte advances and requested transfer spans coincide. This does not establish which transferred palette indices are used, any pixel mode or transparency, current resource selection, or the observed command-HUD association.
