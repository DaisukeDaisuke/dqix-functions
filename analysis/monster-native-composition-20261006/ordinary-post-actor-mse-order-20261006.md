# Ordinary field actor and MSE order, with conditional mode1 reconstruction

## Source route

Formal ROM SHA256: 3c9d809eb8e446b0da6a9b383c7a6c5146001636038384aa49cb1a2e367546d7.
This proof uses bounded ROM source ranges only. It does not use runtime state or
historical render-cycle captures as inputs.

The field wrapper021965e8 calls the world/actor pass02196784 at021966c4 and later
calls the UI/MSE pass02196c80 at02196754. The world pass's guarded map calls
021967ec/021968b8 precede natural actor dispatch021969a8. Its already established
chain is02196f60 →021a2828 →02077b08 →02032b14. The UI/MSE pass calls0207ba90 at
02196f20, after the world/actor pass has returned. Other UI/effect calls exist
between these stages; they are not silently reconstructed or excluded.

The new readNaturalBodyMseOrder guard retains the original natural draw-order
proof and additionally checks021965e8+0x190 and02196c80+0x2e0 plus the three direct
calls. It asserts a conditional source submission order, not live reachability.

## Submission order is not naive image layering

The existing source raster profile uses native opaque sorting followed by
manual-source-order translucent lists. The extension preserves the stages:

1. Native-sorted opaque/binary actor fragments merge against source map opaque
   depth. Equal-depth actor/map ownership remains unknown.
2. Map translucent-list fragments run in original source order.
3. Actor translucent-list fragments run in original SBC/GX order.
4. MSE translucent-list fragments run in the guarded later source order.
5. Fog applies once using the final accepted depth and fog flags.

Alpha31 texels from alpha textures remain in their translucent-list position.
They write depth, opaque ID and fog ownership when accepted. Alpha1–30 uses the
existing blend/ID/depth/fog-AND state machine. The connection never appends an
actor to final fogged RGB and never lets farther map translucency contribute
through a nearer opaque actor. Contract tests cover both of these cases,
MSE alpha31 ownership/depth/fog updates, partial-alpha retained depth, and
unchanged unknown rejection at equal opaque depth.

## D0 source geometry and unresolved state

The formal source plan for all four saved D0 branches requests D04M02E1.mse.
The source constructor geometry has two layers with polygon IDs55 and56,
60 original polygons, alpha-texture formats and depth9179136. Their source
polygon fog bits are set. In the one exact125.846 constructor probe,
positive-alpha MSE fragments cover1133 of1254 pixels in sprite290's ROI and641
of651 in HUD43's ROI. These are source-hypothesis footprints, not a mask of the
observed effect or proof of final fragment acceptance.

0207ba90 requires a loaded layer list, rejects manager+0x11 mask0x01, and calls
0207bfdc,0207c07c and0207be40 before drawing. Each layer's positive zoom gates its
layout, and offsets advance after drawing. Loaded/enable/fade/current-offset
state is not observed. Existing native-mse-initial-preview already represents
constructor offsets as an explicit hypothesis and exposes currentPhaseProven
and gatesEvaluated as false. The new connection supports only the already
retained omitted-effect and constructor hypotheses. Dynamic offset/fade/enable
states remain explicit unknowns; none is fitted or inferred from PTS.

## Missing pipeline connection and its bounded repair

The CPU mode1 renderer already owns opaque pre-fog RGB6665, depth24, original
owner/facing and the map/MSE fragment streams. Previously those were discarded,
and background-branch transport omitted the exact screen-effect hypothesis.
The mode2-only provider could not reconstruct this mode1 destination.

New branch metadata preserves the renderer's source request and exact phase.
The lazy native provider reconstructs the same source camera/environment,
checks the source request/order, replays only the bound hypothesis and binds its
unchanged final background bytes. Raw planes remain private to this native
request. Legacy requests without that metadata keep their former behavior.
A full-proposal unknown destination or equal-depth tie remains a failure.
Known-plane flags describe this conditional reconstruction, not the current
framebuffer, current phase, identity or absence of other actors/UI.

## Bounded measurement

Both complete automatic D0 background searches were rerun from the same frozen
formal frame and unchanged ROM-font evidence. Removing only the new mode1Scene
metadata gives exact equality with every old branch-support field, including
background pixels, masks, alignment, camera and residual evidence.

All24 previously saved best proposals were tested once across all four models
and both background hypotheses. Their separate isolated-body fits, poses and
extent records are exactly preserved. z021a sprite scene gains improve but
remain negative on both frames and both hypotheses. HUD43's z000c support stays
positive. No new finite pose sweep, DINO inference, alpha fit or recognition
repair is claimed. The new result remains conditional and adds no certified AT
clock, identity, phase or minimum-call evidence.
