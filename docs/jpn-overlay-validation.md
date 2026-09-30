# Japanese overlay layout validation

Checked on 2026-09-30 against the supplied `DRAGONQUEST9` image, game code
`YDQJ`, header revision 0. Its SHA-256 is
`3c9d809eb8e446b0da6a9b383c7a6c5146001636038384aa49cb1a2e367546d7`.
This identifies the supplied image only; clean retail identity was not established.
No ROM bytes or decompiled game code are included here.

## Overlay 23 correction

The ARM9 overlay table begins at ROM offset `0x9FE00`. Its 32-byte entry 23,
at `0xA00E0`, identifies overlay ID 23 / FAT file ID 23 and declares:

- Initialized base: `0x021D9300`
- Initialized length: `0x25940`, ending at `0x021FEC40` exclusive
- Separate BSS: `0x560` bytes, ending at `0x021FF1A0` exclusive
- Decompressed initialized-image SHA-256:
  `38ab6501590d25b53fbbeab7b947ca5428373fb1939a946cb48793713e114396`

An independent bounds-checked BLZ decoder matched ndspy 4.2.0 byte-for-byte.
A Ghidra 12.1.4 import through NTRGhidra v1.5.1 with SDK-layout corrections
matched all 35 ARM9 overlay initialized-image hashes, including this image in
`overlay_d_23`. BSS was checked separately as uninitialized memory.

The previous `0x02200940` / `0x4CC40` header described overlay 31. All seven
existing overlay-23 function entries lie within the corrected initialized range.
Containment does not establish a function boundary, execution mode, name, ABI,
behavior, or live residency; existing function annotations were left unchanged.
The two labels at `0x021F52C4` were not disambiguated.

The existing `AT_LCG_Location` entries in the overlay-23 and overlay-31 files
point into main ARM9 RAM rather than those overlay ranges. They are preserved
as cross-references; their names and non-Japanese addresses were not validated.
No EUR/USA metadata was changed or inferred from the Japanese image.

## Release coverage

`arm9_overlay17.yml` already declares Japanese-only symbols with base
`0x0218C1C0` and initialized length `0x4C9E0`, matching overlay-table entry 17.
Its four function entries are inside that range. The release workflow now
generates it under `output/overlay17`, using the same command pattern as the
other overlays; the existing Japanese archive glob includes its output.
The overlay-17 annotations themselves were not modified or revalidated.

Format references: [ndspy code/overlay API](https://ndspy.readthedocs.io/en/latest/api/code.html),
[NTRGhidra](https://github.com/pedro-javierf/NTRGhidra).
