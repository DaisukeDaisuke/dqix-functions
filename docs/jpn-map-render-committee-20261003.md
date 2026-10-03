# JP map rendering and floor annotations, 2026-10-03

These are descriptive reverse-engineering names, not recovered original symbols.
Existing symbols are preserved. Addresses below refer to the JP ARM9 image unless
explicitly qualified by overlay. No ROM, RAM dump, game image, video or extracted
asset is included. Source-derived interpretations and witnessed calls are distinct.

## Provenance and scope

- Committee A placement source r5 and private material-packet-state0 capture:
  static placement/material interpretation and SDK billboard analysis.
- Committee C camera source r6, REPORT.md and CAMERA_CONTRACT.md:
  fixed-point camera calculations and maplist selector interpretation.
- Root scripts capture-native-bmbl-chunks.mjs and replay-native-bmbl-chunks.mjs:
  one original-state 88-byte record's defined fields matched. Padding, runtime
  pointers and uninitialized bytes are not promised to be zero.
- Root capture-native-archive-context.mjs: calculated archive selection from a
  paused state; this is not a dynamically witnessed map-entry load invocation.
- Root native floor replay: one ordered candidate and one caller tolerance branch.
- Root capture-after-second-exit-presented.mjs and callback capture/replay scripts:
  natural input route 7402 -> 7401 -> 7400, no RAM patch; actual callback packets.

## Archive and placement functions

02013E08 / 02013ED4 request and poll texture archives; 02014374 / 020144C8
request and poll geometry/placement streams. Generated-map branches require their
actual current context. Filename existence alone is not proof of active binding.

0201C550 handles BMBL opcode 65 and calls 0201E090 to append a chunk record.
0201E064 gets a bounded record. Record stride is 0x58. Defined fields are ID,
three fixed-point coordinates and bounded string fields at 0x10, 0x20 and 0x30.
The corpus parser observed 667 streams and 754 definitions; these are structural
counts, not rendered-map coverage. The original-state record comparison hash was
f53e9300403f7439dcaab16aec9cc7d9e53cc250d52a225db202de80a2da1391.

## Camera

0202DC14 applies camera projection/view; 0202E148 constructs the ordinary field
camera eye; 0202E7CC forwards orbit state. Exceptional transform, shake, follow,
blend and clamp conditions must remain explicit unsupported paths where unverified.
020A4490 applies an initial preset; arrival at that initialization was not witnessed.
Existing parseMapListFieldRecord at 0209B32C is retained without duplicate naming.

## Floor

0204CD64 initializes COL2, 0204CE50 collects candidates, 02030BD4 is the AABB
predicate, 02030DF4 selects a floor candidate, 020316BC intersects a triangle and
02031BC8 performs the fixed-point plane/segment intersection.

One original-state replay selected record 485 and point [0,653,65536]. Ranking
height 656 and final plane height 653 must not be conflated. In overlay_d_17 at
02193710, plane 653 plus 0x199 gives 1062. CMP 02193A20 and BLE 02193A24 take
02193A3C for delta 2 <= 40, retaining actor Y=1064. This does not justify snapping
all actors to plane+offset. Caller code 1836 bytes matched the imported overlay:
73a5b4ac192b7355dfc6b37b656ae14d5e99bb77f972d49d5b178c51c2cc79f9.

## Map-specific billboard callback

020B6BB8 and 020B6EC0 are SDK full/Y billboard handlers. On the observed outdoor
route, the Y handler invokes 020E4B60, which forwards mode 0 to 020E48F4. The map
callback sets context bit 0x40, suppressing the SDK default body. Substituting the
default billboard packet would therefore reproduce the wrong path.

The mode-0 replay preserved the 72-byte packet template, applied actual model-view
translation and conditionally normalized the cached Y column. The initial dirty=1
packet and two following dirty=0 packets matched native bytes and context flags.
The 640-byte callback code matched ROM:
ce63305c33ec3982d3e8011c991a7d4e2419ceafe5c98a71bbf4b895f18dd30f.

Mode 1, resource correction, independent model-view reconstruction for every
instance, tree identity binding, fog and complete browser/native scene parity remain
unverified. Packet agreement is not a claim that all outdoor objects are rendered.
