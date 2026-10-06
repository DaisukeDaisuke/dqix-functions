# Ordinary field-context reset and allocation branches

Static ROM analysis (ROM静的確認) of Japanese YDQJ revision 0, 2026-10-06 UTC. This is not live-game measurement or proof of reaching a function in the video. Names below are descriptive, not original symbols. Addresses and structure offsets are hexadecimal unless explicitly marked otherwise. Exact ROM/range SHA256 bindings are in `field-context-reset-range-hashes.json`. The inspected bytes came through the existing SDK runtime-layout and overlay17 readers. No live RAM, save state, emulator input, native execution, full-ROM Ghidra import or video-state assumption was used.

## Four-context initialization and allocator binding

ARM9 `02028020` iterates four contexts at stride0x314. It initializes the same scalar/container state described below, but also clears field+10 and starts flags at0. After the reset helpers return, it explicitly assigns the group index0,1,2,3 to the low two flag bits at020280e8. The function ends at020280f4; its following mask literal is included in the range hash.

Wrapper `020280fc` calls this initializer at02028108. It then assigns each context's+10 pointer from the caller's second base plus0x14*index. These are allocator-context references, not evidence that backing allocation succeeded or that its contents are known. The wrapper does not allocate twelve monster objects. Its outer invocation/reachability is unproved here; a map-entry observation must not be treated as a call to this all-context initializer.

## Per-context reset writer: ARM9 0202821c

`0202821c` takes a field-context pointer in r0. Its body ends at `020282a4`; the following word is its mask literal. For a valid, non-aliasing ordinary field object of size0x314, it performs these scalar writes before calling seven in-place container reset helpers:

|Field offset|Width|Value|
|---|---:|---|
|+00|u16|0, map ID cleared|
|+02|u16|old flags AND3; group bits retained, not initialized from an index|
|+04|u16|0; meaning not assigned here|
|+06|u16|0, creation counter|
|+08|u32|0, scheduler timer|
|+0c|u8|0, active byte|
|+0d|u8|FF; meaning not assigned here|

The source first clears flag masks4 and8, then masks byFFFF000F. Since the value is loaded/stored as a halfword, its final value is the old low two bits. The field allocator-context pointer at+10 is preserved. This is not a whole-object zero fill, a reset of every actor, or evidence that a video transition reached this operation.

## Bounded callee effects

All seven calls in this reset closure were inspected. They initialize container contents in place; none is a heap-free operation. The four fill tails resolve via their literal target to `020019c8`, whose direct `02001a48` callee fills exactly the requested byte count, including alignment and byte-tail handling.

|Call target|Passed pointer|Verified writes relative to that pointer|
|---|---|---|
|0202791c|field+14|bytes00..03=0; words04,08,0c,10,14,18,1c=0; word20=1; bytes24,25=0; word28=0|
|0209dd74|field+40|halfword18=0|
|0209d9fc|field+5c|C0 bytes zero|
|0209ccac|field+120|1C8 bytes zero; word1c8=FFFFFFFF; words1cc,1d0=0|
|02070114|field+2f4|0C bytes zero|
|0206ffb4|field+300|word00=0; byte04=0|
|020aac0c|field+308|0C bytes zero|

Consequently the reset does not write field bytes0e..13,3a..3b,40..57,5a..5b,11c..11f,305..307. Those bytes must not be fabricated as zero. The low group bits are carried through the flag store. The inspected reset-callee closure contains no random-number or seed-setter call; that statement applies to this bounded operation on a valid disjoint field object, not to its callers or the whole map loader.

## Allocation/cache function: ARM9 020282ac

The caller’s local branches are distinct:

1. It performs a prelude of calls, then scans all four contexts with stride0x314 for the requested map ID. A match returns0 before the local reset/allocation path. This does not prove the prelude side-effect-free.
2. If no map matches, it selects the first context whose flag mask4 is clear. No free context returns0 before the local reset.
3. For a selected context it calls `0202821c` at `02028350`, then sets flag mask4 and stores the requested map ID. It passes the preserved field+10 allocator-context pointer through `020321c0`, then requests0x1320 bytes through `0203207c`.
4. A null allocation removes the twelve registry slots for the selected index and returns0. The inspected path does not undo the selected context’s map ID or flag mask4. It must not be modeled as the same outcome as a cache hit or no-free-context return.
5. A nonnull allocation walks twelve0x198-byte objects, calls the existing object-reset and inactive-marking helpers, registers them in slots112+12*index+i, and returns1.

Heap reset/allocation behavior, registry side effects and object-reset residual bytes remain owned by their existing contracts. No allocation success, pointer value, native pool contents or initial group-index assignment is inferred here. The reset retains the existing group bits; their all-context initialization is the distinct02028020 operation above.

Observed direct allocation callsites include overlay17:0219e7f0 and0219f3fc. The first branch subsequently looks up the field and explicitly writes timer0 at0219e868, including its returned-zero path when a field lookup succeeds. Therefore a cache hit alone must not be promoted to a timer-preservation claim. The second caller keeps the allocation-result/non-null-field tests separate before starting its loader task. Full outer caller reachability remains unknown.

## Active byte is a later, conditional write

The known graph-resource stage at overlay17:021b507c writes field+0c=1 at021b5134. It reaches this store only after:

- its resource-ready predicate succeeds;
- resource status equals2;
- the returned archive pointer is nonnull;
- the selected `bin` member pointer is nonnull;
- the graph reset, typed graph parser and adjacency constructor calls return.

This stage does not check a parser success return before the active-byte store. Do not replace these exact source predicates with a stronger invented “all loading succeeded” assertion. On the ready branch, including missing/status-mismatched resources, the stage performs resource-release/request operations and advances its loader-state byte; on the not-ready branch it returns without that continuation.

## Completion flag8 is separate from resource success

The loader task initializer is021b4dcc. Its dispatch at021b4e00 reads the source table at021d6fd0; the state8 entry is021b5a0c. This table binding and both functions have separate range hashes.

State8 first calls its resource-ready predicate. If false, it returns without completion. If true, it checks status2 and a nonnull archive pointer. A status mismatch or null pointer skips its resource-building block but still reaches release, task handle=-1 and the completion call at021b5bf0. The source therefore does not make successful resource construction a prerequisite for completion.

Completion function021b5bfc first scans up to four typed party results. For each nonnull actor whose map matches the bound field, it calls the graph node query, writes actor+B8 and sets actor+C2 mask40. These are actor effects, not field/reset writes. If predicate0202c094 succeeds and task+12 is nonzero, it can also call the existing creator021a2bb8 at021b5d7c. The creator's return value is not checked before the completion stores; its other effects remain outside this pass.

After either skipping that optional creator branch or returning from it,021b5d90 writes `field.flags |= 8`, and021b5d94 writes task+1=1. These stores do not set active, reset the timer, or clear the creation counter. They operate on the then-current fields after prior calls and asynchronous loader stages. Flag8 is a completion marker, not proof of successful resources, active1, a zero creation counter, or no actor creation. No native scheduler call or clock cadence is inferred from it.

## Tool qualification and limits

LLVM19’s installed ARM disassembler decoded these bounded ranges without any installation. Linear instruction output includes literal pools, so function boundaries, direct branch targets and literal loads were checked separately. Its ARMv5te profile declines some MUL/MLA encodings in caller index arithmetic. Their encoding/operand interpretation was cross-checked against the installed Ghidra ARM instruction specification and the unchanged bytes decoded under LLVM’s v6 feature; the profile mismatch remains recorded, not silently treated as ARMv5 decoder coverage. The reset body and its seven reset callees do not depend on that fallback.

This is source control-flow and write-set analysis, not executed ARM validation. Prefix stores and post-return reset-callee guarantees are separate: the latter require every listed callee to return normally with valid non-aliasing storage. No same-state live-ROM/C++ first-difference was measured, and this writer is not established as a cause of the current video/replay mismatch. No initializer was implemented. A future conditional reset projection can use the closed writes only with explicit reached-operation, valid field-storage and disjointness preconditions. It must preserve unspecified bytes and keep cache/failure/success outcomes separate. It cannot infer current field activity, loader/reset timing, a seed, initial timer at video time, occupancy, native clocks or AT consumption for the surrounding map transition.
