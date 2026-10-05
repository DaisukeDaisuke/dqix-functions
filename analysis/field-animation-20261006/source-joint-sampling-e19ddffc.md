# Source field animation and sampled-pivot correction

Bounded original-ARM and same-call observations, 2026-10-06 JST. Runtime correction: dq9-AT e05e06ded58e67f3d12525bcfb863d3b4acc616d; optional explicit blend/emission checkpoint: e1104ccd75168bb9d10c2cb27bc4b9307f7c973d (not automatically selected).

Stored J0AC rotation at 0x020ba3e8 distinguishes source rotation references. Sampled pivots reach 0x020ba464 and normalize the third axis using 0x020c49e4. Constant pivots take a separate direct path; stored basis references use the fixed-point cross product. The old JavaScript parser discarded reference bit15, preventing safe branch identification. The paired correction retains this bit per sampled rotation and uses the existing exact fixed-point normalizer only for tagged nonzero sampled-pivot third axes. Zero-axis behavior remains legacy and is not newly certified.

The unchanged fixed set consists of 17 previously failing z007b clip/frame cases and three established controls. All 19 changed node results are one-FX12-unit third-axis corrections; 228 node outputs and 360 original-ARM active-channel checks pass. Translation, scale, inverse scale, flags, other rotation branches and float preview matrices remain unchanged in this set. Old failures are retained. Old parsed native animation caches must be recreated with the coordinated module/runtime revision.

J0AC wrapper0x020b9388 clamps phase to [0,frames*4096-1]; 0x020b96a8-0x020b96c0 uses the lower12 bits and resource+8 bit0 for interpolation. Observed field playback0x02034d5c/0x02034ddc has explicit rates/gates and a reached-leaf period of (frames-1)*4096 with a single source wrap. This is not a seconds-times-frame-rate rule or a video epoch inference.

Default joint blend0x020b5678 evaluates callbacks through0x020b9388. The observed rule2 callback0x020bbe5c uses a shared ordered scale context. Each weighted scale term is truncated before summation; translation uses the source signed64 product. The first/third axes are normalized and the second rebuilt by a rounded cross product. Single-link calls preserve the direct callback path. Unknown mappings/custom callbacks and degenerate scratch-dependent fallbacks remain unsupported.

A same-fixed-state two-link observation matches12 final joint outputs and24 intermediate callbacks. Across297 explicit scenarios,3996 node calls and14463 active-field checks match original instructions. Rule0 implementation is separately guarded; these three model scenarios are rule2. No universal scaling-rule claim follows.

The optional body adapter binds an opaque plan to its exact compiled model/SBC. Already scaled/blended results enter original node emission without scaling twice. All38 packets match original ARM emitter0x020bbd30, then160 vertex submissions and95 polygons match independent-command replay through the shared downstream renderer. Only54 pixels are onscreen at that partially offscreen fixed state. This is not native GPU/presentation parity or recovery of the actual3300s actor state.

The 3300s video clip/phase/blend history, callback/visibility state, full body membership and species remain unverified. No ROM/state/resources, original byte spans, raw channels, geometry, images or secrets are included in this annotation.
