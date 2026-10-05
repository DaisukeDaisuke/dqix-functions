# Observed epoch-to-render clock

2026-10-05. Assignment 91b69fdb-a1f6-43ee-af61-2c90bd51fa32. Source offsets reconstruction is established only on the stated replay; rendered-frame parity and historical-video synchronization remain unverified.

## Implemented

The previous three-site event clock used constructor entry and the successful-return instruction. This new adapter consumes the more precise eight-site `mse-reload-epoch.mjs` history. It reconstructs offsets only after an observed clear → resource request → empty-head build entry → completed nonempty-head build. A reused allocation pointer does not preserve the old phase. Successful, paused, and no-layer common epilogues are distinct. The initial retained epoch has no guessed phase.

The capture observes these four words in order: MSE manager+20 (environment), map object (map ID), manager+16 (control), manager (head). It thus supplies the per-call source environment result needed by the existing ROM-bound state stepper, rather than a hard-coded surface gate. Source rules validate the after-call control/environment transition. The current source stepper still supports the initialized, finite float32, positive-zoom, nonnegative single-wrap, zero-day-mask subset; this work does not broaden that arithmetic domain.

`buildMseEpochRenderClock({binding,stateRules,profile,capture,manager,mapObject,mapId})` validates the epoch stream and returns reconstructed completed calls plus frame-boundary states. A boundary inside a call has `sourceStateReady:false`; source-complete boundaries compare reconstructed offsets against observed layer offsets. Frame-boundary control bytes are explicitly observations, because source control can change outside MSE calls.

`mseEpochCallRenderOptions(clock, completionSequence, {indexStart,rasterProfile})` returns options for one proved completed admitted source invocation. Pass `renderOptions` directly to the existing `buildMsePolygonInputs(project,profile,renderOptions)`. This carries pre-update offsets and alpha from the same call. A paused/no-layer call does not emit draw options. An unknown retained epoch cannot be seeded with manually supplied offsets or an elapsed-time phase. The return is not a statement that these polygons are the currently displayed framebuffer.

`sampleMseEpochNativeFrame(clock,nativeFrame)` returns only an exact captured native boundary. Neither this API nor the renderer adapter accepts video PTS as native frame numbers.

## Measured replay

Inputs are the previously fixed formal ROM/DST and the unchanged 2,780-frame ordinary movement/encounter/two-flee route. No injected RAM, state substitution, random input search, or elapsed-time scaling was used.

- All 2,780 recorded observer-ON rows exactly match the preceding saved observer-OFF rows, including map/control/layers, registers, and framebuffer brightness statistics. No new PNG equality claim is made.
- 212 paired admitted calls, 22 paused returns, 1,005 no-layer returns.
- The 61 pre-clear admitted calls remain unknown-phase.
- All 151 post-build admitted calls reconstruct through the ROM state stepper; 388 source-complete frame-boundary layer states match native offsets.
- Four source calls (fresh ordinals 1, 6, 51, 151) each pass the existing polygon/integer fragment pipeline: 60 polygons and 98,304 fragments. These are renderer execution checks, not comparisons against native framebuffer pixels.
- First post-build source update completes in bucket 2410. Six source updates precede first visible frame boundary 2424 on this route alone. Six is not a universal hidden-update default.
- Rejection tests cover wrong environment-word address, missing event, changed save-state serial, wrong source instruction, incorrect complete-boundary offset, and incorrect native environment output. Unknown retained, paused-call, and out-of-range request behavior is separately exercised.

Private reproduction evidence is under `connect-video-fog-clock-91b69fdb/private/`; it is excluded from every publishable allowlist. The test entry point `check-source-clock.mjs` lives outside the publishable source because it refers to those local fixed inputs.

## Capture and integration

`capture-clock-route.mjs` retains the previous explicit-path capture CLI, changing only the first two observation words to environment and map ID. The ROM/DST hashes and input sequence inside this script are fixture guards, not renderer defaults.

Example capture options: `--rom <private.nds> --state <private.dst> --runtime-module <headless-node-session.mjs> --assets <runtime-assets> --out <fresh-private-directory> --observer on --mode epochs`. Optional `--sharp-module <installed-module>` records the existing screenshot checkpoints. In epochs mode, configure the exact eight sites from `binding.sites`; no instruction is patched.

Load `binding` with `bindMseReloadEpochSource({sdk,overlay17})`, source `stateRules` with `readNativeMseStateRules(sdk)`, and the selected ROM map profile with the existing `readInitialMseLayers(...)`. The module imports the existing `native-mse-state.mjs` and `native-mse-alpha-material.mjs`. The previously added alpha/geometry/raster source dependencies remain necessary; this checkpoint does not duplicate or replace them.

## Unverified boundaries

The historical one-party video and the four-party formal DST are not synchronized. Visible map-entry time, blackout boundaries, and successful source-call ordinals cannot by themselves establish native dispatch/pause/reload history or presentation-buffer timing. `currentPhaseCertified`, `nativeFramebufferParity`, and `videoSynchronized` remain false. Proven AT calls remain zero. No video phase search, seconds×30, guessed first-draw delay, or manual fog parameter input has been added.
