# Signed native scanline widths

Source comparison: pinned DeSmuME 535f676, _drawscanline and _runscanlines. Implementation: dq9-AT 57f3f0ae4f975e1a3c32e9431db295f4f79c311f.

_drawscanline retains rasterWidth as a signed value and tests `while (rasterWidth-- > 0)`. A negative row emits no incoming fragments, but _runscanlines still steps the left/right edges and later positive rows remain part of the same original polygon. JavaScript previously rejected the entire polygon on the first negative width. The correction retains the signed diagnostic row, computes its signed depth step, and naturally executes no pixel loop for that row. The scanlines-only fast path counts positive rows only.

GPU packing must pair this with width <= 0 exclusion before Uint32 conversion. This avoids an enormous wrapped work count without discarding the polygon or changing its ordering. No endpoint swapping, convex hull conversion, or triangulation is introduced.

Evidence: the source component's clip, viewport, sorted vertices, row state, and incoming depth/UV/vertex RGB match the bounded original failures and 128 synthetic variants. Corresponding pinned source ranges are byte-identical; added instrumentation records entry state. UBSan was clean on this standalone component. This is not a complete framebuffer/whole-emulator equivalence claim.

The actual F04 quad has three negative-width rows followed by three incoming fragments. Their texels are alpha-zero in this fixture: recovery means the proposal can be scored, not three visible pixels recovered. The two reference fits remain negative. Across the saved domains, 1693 earlier successful body outputs remain exact; 69 former signed-span failures become scoreable. Bounded-domain best scores remain unchanged. Shared background/destination comparisons and seven GPU packed jobs remain exact. No WebGPU dispatch is certified.

No ROM, texture, projected geometry, video, or private state is included in this annotation. Original failed and successful inputs remain separate.
