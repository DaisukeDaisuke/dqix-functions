# Japanese story/quest flag RAM layout

Verified on 2026-10-02 against the supplied YDQJ revision-0 image, SHA-256
`3c9d809eb8e446b0da6a9b383c7a6c5146001636038384aa49cb1a2e367546d7`.
This identifies the supplied image; clean retail identity is not asserted.
Annotations extend repository commit `9b36be98a1c6b04a415cb15d1664ad7e3d667361`.
No ROM bytes, decompiled game code, save content or runtime memory is included.

## Bases and representation

- `getStoryFlagStartAddr`, main `0205FF20`, returns the literal `02108788` held at
  `0205FF28`. It does not dereference a runtime pointer slot. The returned object
  is the story/chain owner, not the first flag byte.
- Generic story bits used by the inspected event/placement consumers start at
  owner `+0x8C`, hence `02108814` in this image. Main `0206F104` receives this base
  explicitly in r1. For a nonnegative signed bit index in r2 it returns
  `array[index >> 3] & (1 << (index & 7))`. Negative indexes return zero. The
  nonzero result is a mask, not necessarily one; the helper has no upper bound.
- Packed quest records start at owner `+0x2CC`, hence `02108A54`. Main `0206F274`
  accepts indexes 0..203, selects low/high nibbles for even/odd indexes, and
  returns the nibble's low two bits. Main `0206F3B4` and `0206F430` test nibble
  bits 2 and 3 respectively, returning their unnormalized masks. Invalid indexes
  return zero. This check does not assign semantic names to the numeric states.

## Consumer checks

The source path through main `02060DE0` condition kinds 0/1 supplies owner `+0x8C`
to the generic bit tester. Placement opcode 17, main `0206E634`, also reads this
array for packed condition class 1. Class 2 calls `0206FCEC`, which uses the same
array but maps IDs above `0x3FF` to bit `id +0x6FA`; lower IDs are unchanged.

Placement opcode 14, main `0206DD20`, calls the packed-quest helpers. Its selection
also depends on other state and the placement interpreter's ordering. Thus one
story-bit array, one visible scene or one save does not establish city-wide NPC
membership. Stornway's exterior is map 100 / C01; R01 inn maps are separate.

## Scope

All addresses above are runtime RAM addresses, **not save-file offsets**.
Serialization offsets, current flag values, all Stornway placement predicates,
and equality between different saves are outside this verification. The fixed
map-range and NPC-ID predicates at `02099E34` and `02099EB8` are unrelated to these
story/quest flag arrays. A flag-dependent change to eligible NPC membership can
change later controller initialization work; no AT call count is inferred here
from the array layout alone.
