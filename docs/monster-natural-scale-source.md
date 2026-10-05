# Ordinary natural-monster scale: verified source subset

Target: Japanese YDQJ revision 0. This annotation establishes scale conversion and a supported actor-to-GX path. It does not establish the live actor scale or a physical-size rejection rule.

## Source path

| Address | Verified operation |
|---|---|
|ARM9 `0209d7e8`|Encounter scale handler. Integer arguments shift left 12; float arguments multiply by 4096f and truncate toward zero. The low halfword is interpreted as signed 16-bit; zero is replaced with 4096. Only numeric argument types 1/2 are covered.|
|Overlay 17 `021a2d94–021a2dc4`|Signed 64-bit multiplication by the source literal at `021a2e3c` (266 on this revision), addition of 2048, then arithmetic shift 12. Calls the actor scale setter.|
|ARM9 `02036f3c`|Stores the low halfwords to actor offsets `+5c`, `+5e`, `+60`.|
|ARM9 `02035abc`|A supported direct draw sign-extends those three halfwords and sends them unchanged to `GX_MTX_SCALE`, register `0400046c`.|

For converted signed 16-bit encounter value a, the stored/rendered scale is:

s = signed16((a × 266 + 2048) >> 12)

Here signed16 means reinterpretation of the low halfword as signed. The corresponding GX fixed-point factor is s/4096. Missing encounter-table or matching-species entries retain the creator's 4096 default; no current table is selected by this analysis. Later scale writers and other draw dispatches remain outside this result.

[Companion source decoder](https://github.com/DaisukeDaisuke/dq9-AT/blob/72afdb7f2b918e83315b425a7b9ed1d1dd07c1a5/web/monster-source-scale.mjs) verifies the relevant instruction words and literal bindings before deriving candidates. It preserves map/table/condition/species alternatives and the creator default.

## Validation and limits

- All 1,044 numeric scale arguments in the tested ROM agree with the existing source conversion implementation; 18 additional integer/float boundary cases pass.
- All 65,536 signed 16-bit converted values were executed through the actual creator arithmetic and setter using blank synthetic actor/stack memory. Each of the three resulting native halfwords matched the fixed-point model.
- A modified GX-register binding and a wrong overlay identifier are rejected. Returned candidate metadata is detached from its input.

The exhaustive claim applies only to the signed 16-bit arithmetic/setter input domain. It is not exhaustive float conversion testing, execution of every encounter handler branch, validation of all draw paths, proof that no later writer changes the actor, or end-to-end rendering/recognition validation. No live RAM or save-state input is required for this test.

## Missing placement and visibility dependency

A known source scale is insufficient to reject a small image region. Physical comparison still needs the applicable model/variant and pose, exact model-unit normalization, camera hypothesis, conservative 3D placements across all applicable floor intersections, and source depth/coverage/opaque-ownership evidence. The player's floor is not the monster's floor, and a residual's lowest pixel need not be a foot. Translucent/fog composite depth alone is not a hard opaque-occlusion bound.

Clipped, occluded, partially visible, unsupported and scripted alternatives must remain unresolved. No physical-size gate or recognition improvement follows from this scale result alone.
