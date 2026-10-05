# Ordinary field map/body submission and conditional translucency

2026-10-06 JST. Source-only note for assignment a2154b20.

## Guarded route

- Overlay17 021967ec and 021968b8 call 02016614 for main/secondary map submission.
- 021969a8 calls actor dispatch02196f60; 02196fcc calls natural field draw021a2828.
- 021a2a18 calls wrapper02077b08; its02077b34 calls02032b14.
- Exact spans02196784+30c,02196f60+98,021a2828+2bc,02077b08+cc plus the explicit BL targets guard the implementation. The inspected route contains no actor-to-earlier-map backwards edge. Branches may skip draws.

Two complete non-stopping formal-state render cycles also placed observed static map draws before the natural actor, with MSE afterward. This bounded witness does not identify the live route or effect phase of a historical video actor.

## Renderer connection

The supported source profile uses opaque/binary first and manual source-order translucency. Body opaque fragments are native Y-sorted, then merged with map opaque depth only under strict depth inequality; equal-depth ownership stays unknown. Replay map translucency before actor translucency, retaining original polygon IDs and original SBC/GX actor order. Alpha31 texels in A5I3/A3I5 remain in the translucent list. They update depth and opaque/fog state as specified; alpha1–30 uses integer blend, duplicate-ID suppression and retained depth when depth-write is disabled. Apply fog once to the resulting state.

The implementation uses reconstructed pre-fog planes, never inversion or blending into final fogged RGB. It admits a complete body footprint only when destination/order is known and reconstructed final background agrees at every footprint pixel. Requested/unknown MSE, unsupported map owners, opaque depth ties, other-actor occlusion and unobserved routes remain unknown or conditional.

## Bounded measurements

1000 deterministic synthetic sequences /12000 fragments matched unchanged pinned DeSmuME pixel/blend functions for RGBA6665, depth, IDs, fog flag, facing and translucent-poly state. This is not whole-frame raster/UV or all-input parity. Existing opaque Mon and metal outputs were byte-identical.

Four of seven old fixed F04 false placements obtain negative sampled own-model gains; three stay unknown due changed-background footprint intersections. All three current F04 sampled gains are negative too, but the visible object in region25 remains unresolved: incomplete pose/placement search cannot establish absence or reject its species. No classification or AT exclusion is added.

Public source checkpoint: https://github.com/DaisukeDaisuke/dq9-AT/commit/ba95458aaad682b976a14a6e71fd98a3d079c53b . Automatic provider wiring/public browser verification were pending at this checkpoint. Raw ROM/state/video/pixels are excluded.
