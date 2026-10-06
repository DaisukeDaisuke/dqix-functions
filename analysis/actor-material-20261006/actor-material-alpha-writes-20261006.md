# Ordinary actor material alpha: conditional source state

## Finding and applicability

The static z021a field-model materials used by the saved D0 comparison contain polygon alpha31, binary-format3 textures and the polygon fog bit. That makes the existing opaque/binary renderer a valid **conditional stored-material subset**. It does not establish the alpha of the actor in the observed video. The ordinary actor renderer can rewrite every material's polygon alpha from actor state before drawing.

This note is grounded in the formal ROM SHA256 `3c9d809eb8e446b0da6a9b383c7a6c5146001636038384aa49cb1a2e367546d7`, the accompanying bounded range hashes and the existing guarded natural-field draw route. It does not prove that the observed object took this route, that initialization just occurred, or that no later writes occurred. It supplies no alpha estimate or extra fitted alternative.

## Reached source route

The existing `readNaturalBodySceneOrder` source guards connect field actor dispatch021969a8 →02196f60 →021a2828, whose natural-body call021a2a18 reaches02077b08. At02077b34, this calls02032b14. Both ordinary draw branches at02032ca8 and02032d6c call02035488 with the same actor pointer.

02035488 first calls02035424. This requires a nonnull model handle at actor+08 and a nonzero low byte from02036ee0; actor+6c flag1 suppresses drawing. Its flags0x100000/0x200000 also provide an alternating-skip branch. These are runtime conditions, not observations supplied by the video.

At020354e0,02036ee0 reads A=(byte[actor+40]>>3)&31 and B=byte[actor+41]&31. The multiply encoding and the tail call to integer division0200ce08 with divisor31 establish the factor combination A×B/31. The draw then reads the signed halfword actor+A0, passes it through0200c534 and0200c084 with the float4096 literal, combines it with the getter result through0200c698, and converts through0200c4c0. The scalar input fields and the exact helper-call order are source facts; no helper or game instruction was executed here.

The derived integer is compared against the cached signed halfword modelHandle+A2 via0207f968. If it differs, and actor+6c does not contain0x20000000,0203553c calls0207f944. The latter requires modelHandle+54 to be nonnull, calls020b8df8, then records the supplied value in modelHandle+A2. 020b8df8 iterates the model material count at model+18 and calls020b8c50 for each material index. At020b8cb4–020b8cc0, the resolved material record's word+0c has mask0x001f0000 cleared and the supplied value shifted16 ORed in. That is a concrete material-alpha mutation, not a guessed global color or fog operation. The writer itself does not clamp an out-of-domain caller value.

## Initialization and later writers

The natural actor reset020779a8 calls02032990 at020779b0; that calls the common initializer02034620 at02032998. The common initializer establishes:

- actor+40 bits3–7 =31, writes0203467c–02034684
- actor+41 bits0–4 =31, writes0203468c–02034694
- actor+6c flags =0 at0203469c
- actor+A0 halfword =4096 at0203473c–02034740
- alpha-transition storage at actor+70 passed to020019c8 with zero and length8

Together with a freshly consistent bound model/material and no intervening mutation, these values are consistent with an alpha31 draw. They are not evidence of the live actor's state or model-cache history.

Two concrete later routes invalidate an unconditional initialization assumption:

1. Explicit setter02036e74 writes the supplied low5 bits to actor+40 bits3–7. If the model exists and flag0x20000000 is clear, it obtains the combined factor through02036ee0 and immediately calls0207f944. This setter does not read actor+A0; the subsequent ordinary draw applies its own multiplier path.
2. Natural update02077a74 calls02032de4 at02077a88. Its common tail02032fb4 calls0203477c. That updater can return on actor+6c flag0x20; otherwise it obtains elapsed input, tests actor+74 through the floating helper and, when the transition is active, calls0203448c over actor+70 then rewrites byte+40 bits3–7 at020347f4–02034808. The current transition parameters, elapsed history and all caller state are unavailable.

This is a bounded proof of these identified writers and the initialization path. It is not an exhaustive inventory of every write to byte+41, halfword+A0, material resources or model caches. Their current values remain unknown.

## Relation to the saved D0 failure and fog

The saved source program has two z021a materials, both alpha31/light-mask0/fog-enabled, white set-vertex-color DIF_AMB and no GX NORMAL/COLOR commands. Their format3 textures have256 and512 opaque texels respectively. No NSBTA/NSBMA/NSBTP member exists in this inner resource. The material/global contribution masks make these material RGB/alpha/fog fields local to the stored material; transferring the map's GX COLOR tint to the actor is not justified by this path.

The four retained background branches use the existing same-frame source-mode1 fog helper. It validates conditional ROM ordinary fog parameters and uses the actor's own accepted fragment depth. It explicitly does not certify current environment state or infer a live fog phase. No omitted actor-fog writer was demonstrated in this bounded investigation.

All six saved result branches already state that opaque/binary scoring has no scene occlusion, and the completed D0 report states mode1 destination reconstruction is unsupported. Their generic material-unknown alternative does not explicitly identify the actor-alpha dependency. The companion reporting-only patch appends that dependency and the mode1 destination limitation to existing branch metadata. No score, ranking, material, render, gate, scheduling rule or alternative is changed. All24 earlier model outcomes are retained, including negative sprite results and the false-looking HUD conditional prediction.

A nonopaque live alpha would require a separately supported same-frame destination with depth/ownership/fog/order. The current lazy destination provider admits explicit mode2 only; final mode1 background RGB is not such a destination. This missing state is not permission to blend against an invented backdrop, fit alpha to the frame, or claim that alpha caused the observed mismatch.

## Decoder qualification and limits

The installed LLVM19 ARMv5 profile rejects the register-alias MUL/MLA encodings at02036efc and020b8c94. Their bitfields were independently checked against the already-installed Ghidra ARM instruction definitions and the same LLVM library's +v6 decoder. Both agree on the arithmetic. This is an explicit decoder qualification; it is not ARMv5 decoder coverage, a new emulator, Ghidra import, or execution of ROM instructions. Private byte/disassembly artifacts remain outside this semantic package.
