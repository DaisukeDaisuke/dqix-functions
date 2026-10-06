# Ordinary actor draw root: stored position, temporary Y and matrix branches

## Evidence boundary

This is ROM静的確認 of Japanese YDQJ revision0, SHA256 `3c9d809eb8e446b0da6a9b383c7a6c5146001636038384aa49cb1a2e367546d7`. It reuses the already preserved natural actor call route and source ranges, then follows only observed matrix/position callees. Range hashes accompany this note. LLVM decoding and semantic matrix checks are not execution of the game instructions, live-game measurement, or proof of the video actor's state.

## Natural actor draw and temporary Y

The retained natural route reaches `02032b14`, which calls `02035488` at `02032ca8` or `02032d6c`.

`02032b3c` saves entry actor+48 (Y) in r5. The following distinct pre-draw branches exist:

1. At `02032b20–02032b44`, the low15 bits of actor+C4 control a temporary downward Y adjustment. With that value nonzero, a factor is initialized to4096. Values below100 or above4900 use the existing floating helper sequence with literals100.0 and4096.0. At `02032bc0`, `02037218` reads actor+68, the height field. The result is divided by3, doubled, multiplied by the factor with signed64 `SMULL` at `02032bd0`, biased by2048 and shifted right12. That value is subtracted from the saved position's Y, then the XYZ vector is copied to actor+44 at `02032bf8`. The floating helper bodies were not re-executed or exhaustively revalidated in this investigation; this note does not offer an arbitrary-state numeric projector.
2. At `02032bfc–02032c28`, a nonzero actor+124 replaces the current temporary Y. This is an override, not an added offset. It is applied after the C4 branch.
3. A nonzero halfword actor+BC selects another displacement path at `02032c2c`. It uses global camera-basis data at02109d14, signed actor+BA and `02030964` / `020c485c` before drawing. Its timer and amplitude are updated afterward. This is an additional unobserved runtime branch; it is not a source-backed foot anchor.

After the draw, `02032d88–02032da8` tests C4 low15 and actor+124 again. If either is nonzero, `02032dc4` places saved r5 in the temporary vector's Y and `02032dc8` copies it back to actor+44. The copy helper `02013920–0201393c` copies the three words from source to destination. Thus the first two branches can change **draw-time Y while restoring stored Y** afterward. Reading or geometrically proposing physical actor Y alone does not establish the translation sent to the model.

This proof establishes the writes, ordering and conditional restore. It does not establish that any of these branches is active in the observed video.

## Reached initializer defaults

Common initializer `02034620` stores4096 to actor+68 at020346b0, clears actor+44/+48/+4c, clears rotations+50/+54/+58, sets scales+5c/+5e/+60 to4096, clears flags+6c, and writes actor+94=0 at02034720.

Actor initializer `02032990` calls the common initializer. It clears C4 low15 at02032a60–02032a68 and then its high bit at02032adc–02032ae4; it writes actor+124=0 at02032a78 and actor+BC=0 at02032a30. Natural reset020779a8 calls this actor initializer at020779b0. These are conditional post-initializer defaults. Later creation, movement, scripting or effect writes are not excluded.

The explicit height setter `02037210` writes its argument to actor+68; `02037218` reads the same word. Its current argument/history is not available. No exhaustive search of every C4/+124/height writer was performed.

The currently modeled unadjusted draw translation therefore requires, among other conditions, C4 low15=0, actor+124=0 and actor+BC=0. Reaching an initializer sometime in the past does not prove these conditions now.

## Coordinate axes and actor matrix

At `02035548`, actor+6c flag0x8000 skips the transform-submission branch. Otherwise flag0x4000 chooses direct GX helpers:

- `02035a5c` copies actor+44/+48/+4c unchanged to GX_MTX_TRANS register04000470. No fixed model-origin offset or coordinate-sign inversion occurs here.
- `02035a80` applies nonzero rotations from actor+58, then+54, then+50 through02030db0,02030d6c,02030d28.
- `02035abc` sign-extends scale halfwords+5c/+5e/+60 and writes GX_MTX_SCALE0400046c.

The ordinary cached path uses `020351a8`, followed by020b52e0. With actor+94=0 it sends actor+44 directly to the cached translation setter020b531c. The normal branch builds the rotation and sends the scales through020b534c. Actor+94!=0, flag0x80000 and flag0x2000000 expose separate transform/global-order paths; they remain unobserved conditional inputs.

The ROM-initial rotation dispatch table020ef9cc contains the three existing axis thunks in Z/Y/X order. The Y constructor used by the cached path at020c2d4c, and the direct 4x3 constructor at020c3468, both produce the NDS column-major rotation:

`[cos,0,-sin; 0,4096,0; sin,0,cos]`.

This matches the current `projectNativeBodyPolygons` Y-rotation signs. The bounded semantic comparison checked all4096 ROM sin/cos classes. It does not prove zero X/Z rotation or the absence of later transform-state changes. No axis-flip or fixed model-origin correction is justified by this source.

## Relation to geometric root proposals

The native proposal implementation centers the complete source-emitted geometric envelope on the residual ROI and solves its root onto each admitted static COL2 plane. Both facts are labeled assumptions. ROM vertices, source scale, native matrix operations, camera hypotheses and individual COL2 faces are source-derived; the inverse centering equation and which physical root generated the residual are not facts established by ROM.

The existing source ground helper already preserves the02193710 settling branch, including native segment-plane result plus409 and retain-prior tolerance40. Its caller requires actual ordered collision candidates, transforms, admission flags, prior state and movement context. It is incorrect to add409 to an unrelated geometric plane approximation and treat the result as current actor Y.

The companion fixed-root comparison leaves all saved hypotheses unchanged. It confirms that source-ground arithmetic can differ from geometric plane height at identical XZ because the native segment query has fixed-point rounding. That finding is a conditional calculation, not a correction or proof of a rendering defect.

## Implementation boundary

No source edit, root correction, new pose/angle/scale search or inferred current effect state follows from this result. Current generic unknown-Y-writer metadata covers the limitation broadly. Precise additional provenance may name02032b14 C4/+124/+BC draw-time translation, but must not activate those branches without explicit runtime inputs or independent video evidence. A future conditional projector would need those exact inputs and preserve default, adjusted and unsupported branches; enumeration from a field's numeric range alone is not justified.
