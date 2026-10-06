# JP touch-overlay source annotations, recovered 2026-10-06

These are source-derived descriptive names, not original symbols or recovered function prototypes. This small checkpoint preserves analysis supported by existing source-only records. It does not reconstruct a Ghidra database or claim recovery of every previously analyzed function.

## Provenance and preservation

- dqix-functions baseline: `18a9cd0a6cbced6351f43b8914da50c10a022387`.
- Published dq9-AT checkpoints: `experimental/checkpoints/touch-origin-negative-ab633c61/source-only.zip` at `de68aef82e31e707da2f039dce009c6865ad7c4d`, and `experimental/checkpoints/touch-geometry-reconstructed-4d501e2a/source-only.zip` at `30aea6fc31a20306fe19a2351df9f5bbc4ed02f3`, retain the source-only work.
- JP ROM identity: `3c9d809eb8e446b0da6a9b383c7a6c5146001636038384aa49cb1a2e367546d7`. No ROM content is included.
- The original geometry ZIP was lost. The origin reader was recovered byte-for-byte; the composition/raster modules and tests were reconstructed and then revalidated. Their individual source hashes and span guards are preserved in [source-evidence.json](../analysis/touch-overlay-20261006/source-evidence.json).
- Existing symbols are preserved. `__Vector3_Normalize` at `0x020C49E4` and `GetEuclideanDistanceFromVec3Pointers` at `0x020C4AFC` already existed in `symbols/arm9_battle.yml`; no duplicate symbol was added. The retained distance description's “Direct float subtraction” and product-form return line should not be treated as the arithmetic of this touch path: the reconstructed witness uses fixed-point vectors, sum of squared differences and integer square-root/rounding. This note leaves the historical record intact.

## Source behavior supported by the retained records

`0x02098254` loads the four arrow SPR resources. `0x02098330` updates touch-origin/current coordinate bytes; `0x02098474` forwards them through conditional drawing. `0x02098888` emits ring, shaft, head, dot in that order. The ring is offset (-8,-8) from touch origin; the later dot is offset (-4,-4). The origin is screen-space touch start, not a world-space player position.

`0x02098520` contains the distance branch with a source-derived 8-pixel boundary. Distances below that boundary may use actor heading and remain unsupported by the isolated reconstruction. Distances at or above it follow the reconstructed source direction/geometry path. Coordinates and thresholds are derived from source; current input state is not supplied by memory.

The centered shaft/head paths use `0x02048104` / `0x02048268`; uncentered ring/dot paths use `0x020481E8` / `0x02048374`; `0x02048538` emits the quad. Overlay 17 `0x02196CB0` establishes the field orthographic UI subpass. The source-reader checks also cover loaders, defaults, UV conversion and initial animation selection; their ranges must not be interpreted as recovered complete prototypes.

Additional retained helper bindings, without invented prototypes: `0x020C49E4` normalization; `0x020C4AFC` distance; `0x020C4E58` fixed atan table branch; `0x02030A68` angle wrap; the span beginning `0x020307A0` covers source sin/cos; `0x02030910` and Thumb `0x020C3484` cover the Z matrix; `0x020C44B8` projection and `0x020C3BA0` view. Full addresses, guarded lengths and SHA256 values are in the evidence JSON. A guarded span can cover multiple entry points or data and does not establish a function's exact length.

## Bounded measured coverage

The retained reconstructed oracle executed immutable ROM draw code with synthetic source sprite objects and integer division/square-root hooks. It did not use live game RAM, savestates, known player coordinates or actual video vectors.

- 1,960 synthetic vectors, 7,840 polygons, 45,080 exact math/matrix/vertex/UV/color/material comparisons.
- Texture VRAM lower 16 address bits were canonicalized; resource identity was retained by hash.
- Projection and view were separately compared to ROM instruction execution.
- 256 deterministic pairs used the same existing integer raster on independent ROM-emitted versus reconstructed polygons. RGBA, RGB6665, coverage, depth and owner planes matched, with 1,539 assertions and no discarded cases. This is shared-raster geometry agreement, not an independent native framebuffer oracle.
- 74 reconstruction unit assertions. Total retained fresh-run assertions: 46,693. The old 46,704 total is not reused as the new result.

This annotation task inspected existing evidence and checked its identities; it did not rerun those experiments or produce a new actual-video result.

## Explicit unknowns and retained negative result

Current visibility guards, nondefault sprite state, destination depth, fog, capture sampling/photometry, absent-overlay alternatives and observed pixel ownership remain unresolved. There is no all-input proof, automatic video-vector inference, monster-identification success or AT-state inference in this note.

The earlier ring+dot descriptor failed its frozen synthetic positive/negative separation. It admitted no overlay alternatives on its five saved actual frames. Reconstructing the four-polygon geometry does not undo that failed calibration or justify erasing residual components. New actual-video matching requires separate evidence.

No raw ROM, source sprites, projected vertex recipes, rendered pixels, GX dumps, RAM, savestates, decompiled code, Ghidra projects or private archive is included.
