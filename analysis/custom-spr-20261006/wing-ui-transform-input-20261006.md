# Wing ID0: remaining native-pixel transform contract

This is a bounded static continuation of the proved custom SPR loader and direct draw path. No wing template is assembled/rendered, no screen placement search is run, and no HUD mask/species veto is activated. The original64 classification outcomes and native counterexample remain unchanged.

## Source setup located

The only direct initialized-source BL candidate to field function0219b924 is at02196de8, within function02196c80. Its source has a concrete UI-oriented matrix setup:

- Three vectors are supplied through020c72a0→020c3ba0. The first is zeroed by0200f238; the other two come from source literals021d6858/021d6864. In the existing SDK look-at convention they are eye(0,0,0), up(0,4096,0), target(0,0,−4096).
- 02196d10–02196d44 supplies projection arguments top0, bottom786432, left0, right1048576, near−4194304, far4194304, W-scale4194304. In20.12 these are0/192/0/256,−1024/+1024 and W-scale1024. The wrapper020c723c calls020c44b8, writes projection matrix mode0 and loads the result when its source flag1 is set. A unit-W projection would therefore be an unjustified substitution.
- The caller then selects matrix mode2 before later UI draws.

These are explicit source arguments, not recovered current frame state. The exact fixed-point matrix builder includes its own division/rounding and W scaling; no float substitute was introduced.

## Intervening calls remain a bounded dependency

Before calling0219b924, the caller invokes021a45a4, conditionally02071834, and conditionally02044c0c. The first is a wrapper for021a45b4, a64-way runtime UI-state dispatcher. Some cases immediately return; other cases call additional handlers. The current state and the matrix/global render-state preservation contracts of all reachable handlers have not been established by this bounded inspection. The64-case subsystem is not expanded into a general UI project.

0219b924 itself checks runtime eligibility flags before the ID0 branch. Its source placement bytes+3424/+3436 remain unknown. The object's constructor defaults scale words+34/+38/+3c to4096, color word+80 to7fff, and field+82 to31. Subsequent/current values, current global viewport/fog/blend/depth state and draw occurrence remain unobserved. The direct cell consumer proves which object fields it reads; it does not prove their current values.

## Consequence

The format3 cell and conditional flag1 are grounded, but an exact source-native pixel template additionally needs the active projection/modelview preservation and relevant render-state contract. Running a256×256 placement sweep over a presumed32×32 default template before closing those inputs could fit an unsupported transform. No such sweep was run. The next concrete dependencies are the listed pre-wing callers and their state-preservation contracts, or an explicit source-supported reached-state precondition; no manual ROI, label, scale or tint can substitute for them.

Allocation size, requested palette-transfer span and actual runtime execution remain separate. The earlier callee proof establishes a32-byte request for fuki_com count8, not an observed upload in either D0 frame. The structural parser remains an isolated tool and has not gained a renderer or UI classification output.
