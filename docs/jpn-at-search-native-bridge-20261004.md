# JP native AT selection/search bridge, 2026-10-04 JST

A new bounded original-ROM emulator observation starts from a previously hash-verified naturally generated F06 State and holds Down for180 frames. Observer-off/on both CPU registers and seed agree at six30-frame boundaries; final4MiB RAM and framebuffer hashes agree. Twelve non-stopping events have continuous sequence and zero drops; all observed instruction words match the original SDK source. This is not physical DS evidence or a boot-origin reconstruction.

Source/observed addresses:
- UpdateAT0x02003C30 / return0x02003C54: two paired LCG calls.
- ATRandInt0x02031EA8 / return0x02031EF4: maxima1 and17, integer results0 and4. Callers0x02075150 (table choice) and0x020751A0 (weighted choice).
- SelectFieldEncounterTableByNodeAreaAndTime0x02075050 returns to0x02074DC8. One eligible table still consumes one draw.
- ChooseFieldMonsterId0x02075168 receives the table pointer in r0, not the older uncertain r1 annotation. Event-time table header at0x020FDB08 gives table20/count4, flags0x00E06C60. Existing enc table max17 agrees; output8413 maps to integer4 and species88, observed at0x02074E20.
- Creator caller return0x02074EBC returns slot115. No additional observed AT call separates weighted selection and this return; later rendering/actor identity is not certified.

The local origin0xE70A4A02 advances to0x417A4F13 then0x20DDA550. Feeding exact native outputs16762/8413 into the unchanged streaming index search over declared indices1..65536 retains index2 only. Replacing these privileged outputs with the actual one-table predicate and table20/species88 retains23126 candidates including index2. The bounded native-to-search bridge works; a recognized species alone does not identify AT. Finite-window uniqueness is not global uniqueness. Existing candidate reader accepts the saved bounded result; no tracking ledger or video state is advanced.

Earlier original-State no-input180 frames yielded zero calls. The first F06 capture produced two rather than the planned eight; that failed target is retained and the same measured two draws were separately validated. The first table-memory comparison used an after-run snapshot; a separate same-trajectory profile then captured table id/count/flags at event time. These are distinct evidence stages. Raw State/RAM/trace/pixels and tools remain private, outside this repository.
