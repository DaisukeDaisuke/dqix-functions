# JP field lighting first difference — 2026-10-04 JST

Source/native distinction is mandatory. These annotations name observed behavior;
they are not recovered original symbol names or a declaration of complete rendering.

The unchanged original ROM was executed from the fixed shrine state by natural
movement through maps7402,7401,7400 to20006. A paused generated checkpoint preserves
that route. Later stopping measurements are not non-interfering AT timing evidence.

Pinned emulator core535f676 provides the normal/lighting arithmetic. Four transformed
light directions and four half vectors match the captured geometry state. An initial
source-material comparison finds1490 exact position/UV keys, but none of their colors
match. This failure remains preserved; no display threshold was tuned to hide it.

First mismatch: F06M0400.nsbmd, shape2/material1, normal[0,511,0]. Source diffuse/ambient
word2529FFFF predicts RGB6[49,39,23], whereas the native vertex is[39,51,35]. At the
020B60AC handler, an exact model-header match identifies the resource, and the loaded
material word is41E5FFFF. At020B8598, the actual00293130 packet sends41E5FFFF. Feeding
that observed input into the unchanged lighting formula reproduces[39,51,35].

This establishes an input discrepancy and one corrected-input replay. It does not yet
establish the instruction that first changed the material. Do not hardcode41E5FFFF
into other materials, maps, time phases or observations.

The source static loader02014B4C registers020E4B60 for SBC command8 and conditionally
calls02052B60. The latter contains environment selection and color-bearing display-list
parameter modifications, including overlay calls. Its relationship to the observed
material-word mutation still needs writer/branch observation. Unknown overlay context
and decompiler call arguments must not be silently filled in.

Reproduction artifacts: capture-natural-field-checkpoint.mjs,
capture-field-before-swap.mjs, read-field-gpu-vertices.py,
compare-field-native-lighting.mjs, capture-field-material-first-difference.mjs,
field-first-color-runtime-material-replay.json. Raw states, RAM, game assets, vertex
data and images remain private and are not included in this repository.
