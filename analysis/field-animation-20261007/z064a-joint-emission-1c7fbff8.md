# z064a stored joint values and original ARM emission

2026-10-07. Current implementation: dq9-AT6519b08fbc49a47e5a6b2ca454d3fe89619dc408 (adc38d4 is a documentation-only successor). This is a bounded source verification, not live video state recovery. No numerical difference or correction was found.

## Explicit scope

z064a field model, appear frames0–20 and attack0a frames0–17. Same actual ROM resources feed unchanged original ARM and current source. Synthetic zero-initialized ordinary rule2 context, direct node mapping, one animation link and weight4096 are supplied explicitly. No live actor state is inferred.

Original one-link blender0x020b5678 invokes J0AC sampler0x020b9388 and rule2 scaling callback0x020bbe5c. All39 frames/585nodes agree with the current canonical owner-bound plan:5967 active pre-scale values,8307 active post-scale values,3042 cumulative scale/inverse values, and flags. Direct-sampler, one-link and emission checks are separate stages over the same585cases, not three independent pose sets.

The unchanged node emitter0x020bbd30 produces1794packets/8307parameter words identical to the current JS emitter. Packets are captured at sink-entry0x020b8598; the sink is intercepted after its arguments are copied. The video's diagnostic appear5, appear3 and attack10 cases each have15nodes/46packets/213words.

Three negative controls changing an active sampler value, canonical-plan value and emitted word are detected. An earlier control changed an inactive field and correctly produced no active-field difference; that test expectation failure is preserved separately from the corrected control. Original inputs are unchanged.

## What this does not establish

Sink buffering, root/world/camera setup, SBC stack execution, geometry hardware, projection/rasterization, native pixels, current clip/phase, unknown callbacks/remaps/blends, fractional phases or other models remain outside this result. Earlier old-code parity is not promoted to original-ARM proof. No culprit is identified downstream merely because verification is missing. The four fixed video placement/action controls remain unknown with zero additional AT proof.

The source-only reproducible harness is preserved separately from private original resources, raw values, packets and instruction/write traces. No ROM/RAM, raw disassembly, extracted geometry or images are included in this annotation.
