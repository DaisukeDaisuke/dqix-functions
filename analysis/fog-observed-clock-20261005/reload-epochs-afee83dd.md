# Observed fog reload caller and layer epoch

2026-10-05. Task afee83dd-3f56-4df0-b3da-644b2b7b1109. Source-only helper: DaisukeDaisuke/dots-tools, `ghidra/fog-observed-clock-20261005/reload-epochs-afee83dd/`.

## Resolved native path

The fixed formal ROM/DST battle-return route first checks state10 owner flag `0x400` at `0x021b83d4`, bucket 2349. The flag is clear, so the conditional reload call at `0x021b83f0` is skipped. An independent ordinary field lifecycle path then performs the reload:

`0x0219f390` → `0x0219dd44` → `0x0219e49c` → `0x02013604`, bucket 2355.

Both caller links were observed through their native LR values. The reload invokes MSE constructors through `0x020135a0` and `0x02013708`. Observing the constructor's semantic completion endpoint `0x0207b448` confirms an empty field layer head and a clear pause flag.

The resource-request endpoint `0x0207b4d0` is observed in bucket 2388, with loadState 1. The layer builder `0x0207b56c` enters with an empty head and reaches semantic completion `0x0207ba68` in bucket 2391 with a nonempty head. That head reuses the exact initial allocation address. Pointer equality therefore does not establish retained phase.

The first post-build completed MSE update is bucket 2410. Between frame boundaries 2392 and 2410, layers exist and pause is clear, but layer offsets remain zero and no MSE update is observed. Six complete updates precede the first visible post-return frame boundary 2424. These are measurements of this route, not universal hidden-update constants.

## Implemented consequence

The new helper binds 41 ROM instructions and derives epochs from the observed clear → request → empty-head build entry → nonempty-head build completion chain. It counts actual successful common epilogues, preserves the initial retained epoch as unknown, ignores other managers' constructors/builders, and keeps in-flight boundary calls explicit.

The fixed route has 61 paired completed updates before clearing and 151 after the fresh build: 212 admitted, 22 paused, and 1005 no-layer calls. One initial unpaired epilogue is retained explicitly because the formal saved state starts inside MSE.

The completed-update ordinal is suitable as measured native event history. It is not sufficient by itself to certify a RAM snapshot captured midway through a multilayer update or the currently displayed framebuffer. `currentPhaseCertified` and `videoSynchronized` remain false; proven AT calls remain zero.

## Validation and limits

Fresh caller ON/OFF and epoch ON/OFF runs agree on all 2780 recorded per-frame rows. All 18 saved PNGs agree byte-for-byte in each compared run. The row data also agree with the preceding native-final OFF evidence. The portable, explicit-CLI-path harness was separately rerun and agrees on all 2780 rows; that additional portable run saved no PNGs. Ten negative/boundary tests pass.

The owner-flag `0x400` writer and native true-branch coverage remain unresolved. Bounded additional scans do not prove that the bit is permanently zero. The independent reload path means this uncertainty does not block identifying the actually observed reset/build epoch.

The formal DST has four party members; the supplied historical video has one. Their source epochs and event histories remain independently unaligned. This work does not assign elapsed video time, assume seconds×30, search a visual fog phase, or certify automatic video-entry-clock rendering. Full automatic recognition/AT recovery remains open.

No ROM, RAM/state dumps, trace payloads, screenshots, videos, or extracted game assets accompany this annotation or the source helper.
