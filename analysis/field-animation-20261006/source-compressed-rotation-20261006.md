# Stored compressed rotation: bounded ROM source confirmation

Japanese revision-0 ROM SHA256: `3c9d809eb8e446b0da6a9b383c7a6c5146001636038384aa49cb1a2e367546d7`.

This follows the existing `source-joint-sampling-e19ddffc.md` annotation. Evidence here is **ROM static inspection plus direct semantic arithmetic replay**, not execution of the original instructions and not live-game measurement. It does not prove that the video actor reached this clip/frame.

## Observed source path

- Sampled rotation dispatch at ARM9 `020ba3e8..020ba404` loads a halfword reference and calls `020ba7c0` with the rotation-table bases. Return value 1 enters basis cross-product completion; return value 0 enters the sampled-pivot third-axis normalization at `020ba464`, which calls `020c49e4`.
- Helper `020ba7c0..020ba914` tests reference bit 15. Its low 15 bits index either a six-byte pivot record or a ten-byte compressed-basis record. The masking constant and pivot table pointers occupy the following bounded literal pool through `020ba928`.
- Pivot branch `020ba7cc..020ba874` clears nine output entries, uses the record's low-nibble pivot selector, and uses flag bits 4, 5 and 6 for the three sign choices. Position lookup uses the 36-byte table at `020e9390`; this is the animation pivot table, distinct from the model-node table at `020e936c`. It returns 0.
- Basis branch `020ba878..020ba914` reads signed halfwords. Arithmetic right shift by 3 supplies five signed 13-bit components. The low three bits of words 4, 0, 1, 2 and 3 are packed through the source signed shifts and sign-extended to 13 bits for the sixth component. It returns 1.
- Caller `020ba408..020ba460` forms the third vector with six multiply operations, pairwise subtraction, and arithmetic right shift by 12 before three stores. No sampled-pivot normalization is taken on this basis return path.

LLVM19's primary ARMv5 profile reports the six MUL encodings as unknown. These exact encodings were separately checked against the installed Ghidra ARM MUL specification and a secondary LLVM interpretation. That qualification is retained separately; it is not counted as primary ARMv5 disassembly coverage or instruction execution.

## Same saved z021a record check

The bounded witness uses only the already-saved `_f` `appear.nsbca` frame 5, whose source animation SHA256 is `d595f815a5d288eefc114defa051be9dd9698a2ef625a318d0f9732fcf194aa5`.

Node 3's sampled reference is at member-relative offset 1854. It selects a non-pivot ten-byte basis record at offset 732, SHA256 `49f851584c81677b4d145a67da0e76eb5493550b8e0db55a75f3a64c9946d44e`. A direct, instruction-ordered semantic replay reproduces the first six components from the record. The current native third vector equals the caller's signed cross-product ASR12. The preview's third vector instead retains the fractional cross products. The three differences are less than one FX12 unit; this is the precision difference identified in the earlier transform comparison, not a demonstrated decoder defect.

At the same saved frame, all 11 non-identity rotation records were checked: seven basis and four pivot records. The pivot reconstruction matches the parser before the separately annotated sampled-pivot normalization. The normalizer was not re-executed in this task. A separate 72-case structural table/sign comparison covers nine source selectors and eight sign combinations; these are synthetic algebra checks, not new pose searches or actual actor states.

## Boundary and result

No correction to the current basis/pivot reconstruction is indicated by this bounded source evidence. It does not establish the complete animation wrapper, translation/scale channels, blending, current clip selection, current phase, actor root, material/fog state or native pixel parity. The prior eight hypotheses and all video/recognition failures remain unchanged. Raw animation records, SDK bytes and full disassembly are excluded from this annotation.
