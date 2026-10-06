# Custom SPR structural loader and field table

Static source analysis of Japanese YDQJ revision0, 2026-10-06 UTC. This is not game execution, a current field-state observation, or an identification of the command-HUD fragment in video. Names below are descriptive. Exact source range and ROM hashes accompany this note. Raw source bytes, linear disassembly, palette/texture payloads and video remain private.

## Field table and loading

Field-overlay17 function0219c1b0 formats data/bin/icon.nsarc, mounts the archive, and uses the table whose base is021d69bc (literal at0219c418). The loop at0219c260–0219c2b0 reads eight-byte entries: a signed nonnegative ID and filename pointer. A negative ID terminates it. The table has16 entries before its−1 sentinel at021d6a3c. ID0 names wing1.spr; ID2 names fuki_com.spr; IDs15/16 name fuki_com_q.spr/fuki_com_apc.spr. These are source resource associations, not observed UI associations.

The destination is caller-object+2980+ID×88. The loop calls02047fbc to initialize that object, resolves ARC:<filename> through020b17d8, and calls02048960 when the member pointer is nonzero. The destination initializer clears fields, starts source type3, sets three scale words to4096, field+82 to31 and field+80 to7fff. These initialization values are structural evidence only; rendering interpretation is outside this parser.

## Exact serialized read sequence

ARM9 loader02048960–02048cf4 uses byte-copy helper0200195c, whose loop copies exactly the requested byte count without transformation.

1. Read little-endian u16 at0 into destination+6 and u16 at2 into destination+4. The first is the outer frame-loop count; the second controls payload length.
2. Each outer frame starts with8 bytes. The first two u16 values are narrowed to bytes at frame-record+0/+1. The following u32 is narrowed to its low byte for the cell-loop count. Each runtime frame record is8 bytes.
3. Each cell begins with four little-endian u16 values. The last two are register shift counts for extents8×2^shift. Their product is the source area. Type3 transfers/advances area/2 bytes; other source types use area bytes. This describes byte spans only. It does not establish a pixel mode, texel ordering or transparency.
4. The source packs the size shifts into a16-bit word and packs an X-like offset plus a vertically inverted offset (frame byte-height minus serialized Y-like offset minus cell height) into another16-bit word. The reader preserves both raw fields and exact packed results. No viewport or observed screen origin is derived from these fields.
5. After all frames, read u32 palette-count field. The source upload helper02048d90 receives32 bytes if that value≤16,512 if≤256, otherwise the raw value. Separately, the serialized cursor advances count×2. Both spans must be preserved. In actual fuki_com.spr, count8 advances16 bytes while the source upload span is32 bytes and overlaps the following opaque header. The reader does not silently pad or replace those extra source bytes.
6. Skip an opaque30-byte header, read a u32 layout count, then copy that many opaque30-byte records. Helper02048eb0 allocates count×30 bytes. The reader does not invent meanings for these records.
7. For each layout, read a u32 key count and then three consecutive planes of count little-endian32-bit words. The native loader interleaves their words into12-byte runtime records. The parser preserves plane spans and counts without guessing key meaning, time or active layout.
8. Native loading does not check a final EOF marker. The reader reports consumed and trailing lengths; all four measured resources consume exactly their whole source member.

Memory-safety support limits reject truncated spans, signed-negative layout counts and wrapping shift/product cases rather than treating native wrap as a meaningful texture. These are explicit unsupported structural inputs, not recognition thresholds or a claim that every native-invalid stream behaves the same way.

## Placement lead and unresolved observation

Within overlay17,0219bb38–0219bb58 reads caller-object bytes+3424 and+3436, shifts them left12, and positions the ID0 object at+2980 through02039ec4, with the third argument−4096. The setter writes three words to object+1c/+20/+24. The following calls set its state and invoke02048374. The surrounding runtime eligibility flags and those coordinate bytes are not observed in the D0 video analysis.

Thus the current record proves a loader, an ID/resource table, and a placement consumer. It does not prove that wing1 is the command fragment, that this branch ran at125.846s, or which cell/layout/coordinates were current. The direct renderer02048374 and its texture/alpha/layout contract remain outside this structural parser. No HUD mask or enemy-absence deduction is justified by filename, table membership or glyph similarity alone.

## Bounded validation

The reader consumes wing1.spr (694 bytes), fuki_com.spr (1158), fuki_com_apc.spr (1174) and fuki_com_q.spr (1174) exactly. Their source-derived frame/cell/layout counts are retained in the private scalar result. All4200 strict truncation prefixes fail; synthetic tests include byte-narrowing semantics, independent payload ownership, non-type3 byte-span behavior, trailing bytes, malformed counts/shifts and2000 bounded random malformed inputs. No sprite is rendered by these tests.

The already-installed LLVM19 ARMv5te disassembler was used on explicit ROM ranges. As in the earlier field-reset trace, its v5 profile declines some multiply-register encodings. The same unchanged words were separately decoded using LLVM's+v6 feature to verify MUL/MLA operands. The discrepancy is recorded privately; no native ISA or runtime was changed.
