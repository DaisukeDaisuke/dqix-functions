# Natural-field animation phase inputs and the missing lattice bound

## Finding

The reached initializer does not establish integer-only animation phases. The
natural update route consumes a subsequently produced clock ratio, descriptor
start/end/rate values, and a mutable signed actor rate. No smaller fixed FX12
phase lattice is justified for the saved D0 video inputs from the available
ROM/source evidence. This is not proof that every SDK-admissible phase is
reachable in an actual actor history, and is not a reason to treat arbitrary
phase samples as observed state.

## Reached natural caller and dispatch

Natural update 0x02077a74 calls the common actor update 0x02032de4 at
0x02077a88. The common tail calls 0x0203477c at 0x02032fb4. That updater returns
when actor+0x6c has bit0x20; otherwise it obtains scaled elapsed input through
0x0200f25c and 0x02010064, updates eligible transitions, then calls 0x02034894.

The dispatcher 0x02034894 requires actor+0x10, uses that component's bit0,
and reads the two-entry function/adjustment table at 0x020ef9d8. Its source
entries select 0x020348ec for kind0 and 0x02034bd4 for kind1; both adjustment
words are zero. Thus the kind1 selector reaches the already implemented
0x02034d5c / 0x02034ddc arithmetic leaves without inventing a new caller.
The current actor's component kind and actual reached invocation remain unknown.

## Clock producer, not an integer tick assumption

At 0x02034bdc and 0x02034be4 the kind1 updater obtains the registry and calls
0x02010074, which reads registry+0x3c0. The return is narrowed to signed16
at 0x02034bec-0x02034bf0. This ratio is distinct from the pending/phase counter
at +0x3c4 returned by 0x0201007c.

Initializer 0x0200ff80 sets +0x3b4 and +0x3b8 to33, the signed scale at
+0x3bc to4096, +0x3c4 to2, and +0x3c0 to8192. Those are initial values,
not an invariant of later invocations.

The bounded producer 0x0200ffac clamps its explicit 64-bit elapsed argument
at50000 source units, divides by1000, applies the signed scale/4096 using
float32 operations, and truncates to a scaled integer delta. It then computes
trunc(float32(4096 * float32(scaledDelta / 17))) into +0x3c0. The constants
are tied to source literals 0x02010058/0x0201005c/0x02010060 and the immediate
divisor at0x0200fff8. Negative scale behavior is outside the preserved checked
arithmetic helper and stays unresolved.

The inspected overlay17 caller at0x0218d3b0 has an explicit branch supplying
zero to0x0200ffac at0x0218d3cc. Its other branch obtains hardware ticks,
subtracts the retained prior tick pair, multiplies the difference by64000,
divides by the literal33514, and calls0x0200ffac at0x0218d470. Invocation order,
tick intervals, branch selection and mutable scale are not provided by these
video frames. No timer subsystem expansion or timing inference was performed.

A single explicitly hypothetical input of33000 elapsed source units with the
initialized scale4096 produces scaledDelta33 and clockPhaseFx7951. At explicitly
unit clip and actor rates, the existing guarded phase leaf returns delta7951,
whose lower12 bits are3855. gcd(7951,4096)=1. This disproves an integer-frame or
coarser divisor-of4096 grid derived solely from initializer defaults. It does
not bind33ms, either rate, that phase, or any phase sequence to this video. No
phase history or state domain was enumerated.

## Descriptor and actor-rate producers

The descriptor lookup at0x02034210 indexes36-byte records. The kind1 leaf
reads descriptor+0x18 as clipRateFx and actor+0x7c as signed16 actorRateFx,
then performs the two rounded FX12 products already modeled by
advanceFieldAnimationPhase. The forward/reverse leaf updates actor+0x1c;
its period is (frames-1)*4096, with one source wrap or a conditional clamp.

The loader callback at0x02033e2c converts three supplied descriptor values by
float32 multiplication with4096 and truncation into offsets+0x10,+0x14,+0x18.
0x02034154 copies those fields into the36-byte descriptor. The clip request
kind0 branch at0x02036994 selects descriptor+0x10 or+0x14 according to the reverse
flag. Crucially, kind1 differs:0x02036a28 starts forward at0, while
0x02036a30-0x02036a38 starts reverse at(frames-1)*4096. Those integer endpoints
are conditional on this actual clip-switch branch, not proof of a later integer
phase after clock-driven updates. The kind1 rate still comes from descriptor+0x18.
The exact current descriptor/clip lookup and rate were not reconstructed for the
saved actor in this task. Source filenames alone cannot supply them.

The common reset0x02034620 initializes actor+0x7c and+0x7e to4096 and+0x80
to0; common actor initialization0x02032990 calls it. However, 0x0203477c's
0x02034830-0x0203487c branch can write +0x7c while consuming the transition
target at+0x7e and remaining duration at+0x80. Setter0x0203707c either writes
current/target immediately and clears duration, or stores target/duration for
that transition. The direct-call scan found rate-setter candidates in other
actor paths but did not establish their reachability for this natural actor;
there is no claim of global writer exclusion. The source phase setter at
0x02036bfc and descriptor-relative setter0x02036c14 likewise prevent treating
reset0 as a proven persistent phase without caller history.

## Preserved unknown branches and stopping boundary

- The actual reached reset, current descriptor/component lookup and kind.
- Current phase and prior phase writes; reached kind1 integer endpoint reset versus
  retained phase; descriptor clip rate and actor rate.
- Actual hardware elapsed history, source clock scale and zero-clock branch.
- Actor bit0x20 update admission; kind1 pause bit0x1000; valid current clip and
  resource; blend duration+0x38; first-tick flag; +0x40000/string admission gate.
- Reverse/clamp flags and invocation count/order; any transition or explicit
  rate/phase setter relevant to the actor's actual path.
- Callback/blend/visibility and actor identity in the image, independently of
  the arithmetic phase input.

The missing observation is the bound reached clock/rate/descriptor history.
The source route supplies no valid reason to prune the phase search to integers
or a new coarser grid. Conversely, SDK interpolation support and this counterexample
do not certify all phases as reachable. No production code, gates, scores,
proposal rankings, inputs, or historical failures were changed. No RAM, DST,
video-time clock substitution or live-state capture was used.

## Evidence scope

The associated range/hash JSON binds the directly inspected formal-ROM ranges.
Linear instruction dumps and raw bytes remain private. Existing authored clock
producer documentation was used only for its ROM arithmetic mapping; its historical
native observations were not imported as inputs or repeated here. The proof uses
the original guarded field leaf and one pure arithmetic example, not a new emulator
or recognition run.
