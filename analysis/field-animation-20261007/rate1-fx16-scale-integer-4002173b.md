# J0AC rate1 FX16 scale pairs at integer phases

Bounded same-ROM source interpretation, 2026-10-07 UTC.
ROM SHA256: 3c9d809eb8e446b0da6a9b383c7a6c5146001636038384aa49cb1a2e367546d7.
This annotation contains no original bytes, disassembly, raw channels or geometry.

## Source boundary

The wrapper at 0x020b9388 clamps phase before calling 0x020b966c.
The dispatcher at 0x020b96a8..0x020b96c0 chooses integer versus fractional
sampling using phase's low 12 bits and resource flag bit 0.
Its scale calls are 0x020b9cdc for integer sampling and 0x020b9ed0 for
fractional sampling. Each axis supplies one descriptor and a resource-relative
pointer to interleaved scale/inverse-scale pairs. Sampling precedes the
model-scale callback at the dispatcher's end.

At 0x020b9cdc, descriptor bit 30 selects the rate1 branch when its two rate
bits are nonzero. Descriptor bit 29 selects signed16 pair members. The admitted
implementation is narrower: start 0, rate exactly 1, width exactly 2, even
numFrames and end = numFrames - 2. The routine does not provide justification
for adding generic start-offset, rate2/3 or other-width support in this patch.

For that admitted shape:
- Even frame f selects pair f / 2.
- Odd f <= end selects the two neighboring pairs. Each pair member is read
  signed16, added, then arithmetic-shifted right by 1. Scale and inverse are
  computed independently. Negative odd sums round down; the inverse is not
  derived from the scale result.
- Odd f > end selects pair end / 2 + 1 directly.

The width-specific loads and sum-before-shift operation are at
0x020b9e68..0x020b9e98. Direct pair selection is at
0x020b9e28..0x020b9e5c. Tail selection is at 0x020b9d14..0x020b9d20.

## Affected resource and validation

z019b_f attack0a.nsbca is 3,336 bytes, SHA256
21d864152589883214645a9a13b2609d0552241d161daaef3b587e5e52fb257d.
It has 28 frames and 12 objects. Object 8's three scale descriptors are
0x601a0000: start 0, end 26, rate 1, width 2. They share one pair stream.
Frame 26 selects pair 13; frame 27 selects pair 14, not a repeated frame 26.

Unchanged original ARM was executed in Unicorn with explicit resource bytes,
blank synthetic memory and hardware division/root register behavior.
All 28 frames match: 84 scale-leaf calls / 168 pair values and 336 complete
joint-dispatch calls / 3,612 active channel values before model-scale callback.
The separate synthetic set covers 28 invented resources and 12,840 scale-leaf
calls / 25,680 pair values, including signed16 extremes and frame counts
2, 4, 6, 8, 28, 510 and 512. These are not additional actual game resources.

The existing source guard includes the integer and fractional scale leaves:
span address 0x020b9a20, length 4,508, FNV-1a 1777849913.
Verification exports fresh initialized SDK segments from the formal ROM;
no live RAM state or external model oracle is supplied.

## Fractional limit

0x020b9ed0 reads original neighboring pairs, uses interval-dependent phase
width and shift, and handles the final-frame resource-wrap flag separately.
An interpolation of already expanded integer values is not accepted as an
equivalent implementation. The patch therefore rejects every fractional phase
for the new resource kind and does not enqueue it in the fractional domain.
Integer expansion does not certify live clip/phase selection, blending history,
model callback state, video body agreement or whole-input automation.
