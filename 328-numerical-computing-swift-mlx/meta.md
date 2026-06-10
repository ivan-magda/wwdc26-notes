# Session 328 — Explore numerical computing in Swift with MLX

- **URL:** https://developer.apple.com/videos/play/wwdc2026/328/
- **Duration:** 14m
- **Speakers:** David Koski (works on MLX Swift)

## Description

A tour of numerical computing in Swift using MLX Swift. Where MLX Swift fits among
Apple's existing numerical frameworks (Accelerate, BNNS, Metal Performance Shaders,
Swift Numerics), and why you'd reach for it: writing mathematical code that reads like
the math, with automatic GPU execution and automatic differentiation. Core concepts
(n-dimensional arrays, lazy evaluation, compute graphs) are introduced via power
iteration, then applied to three worked examples — the Mandelbrot set, steady-state
heat distribution (Jacobi and SOR), and curve fitting with gradient descent — before a
survey of the broader MLX toolkit and Swift ecosystem.

## Key topics

- MLX Swift's place in Apple's numerical stack vs Accelerate / BNNS / MPS / Swift Numerics; pick it when math-like code + performance is the goal
- N-dimensional arrays as the central abstraction (NumPy-like API; most NumPy translates with minimal changes)
- Lazy evaluation — operations build a compute graph; nothing runs until `eval` or a value is read; call `eval` per loop iteration to keep the graph small
- Two consequences of array computing + lazy eval: automatic GPU execution (GPU by default) and automatic differentiation (`grad`)
- Power iteration walkthrough — `.T`, `matmul`, `norm` map directly to math
- Mandelbrot — array computing over a grid of complex numbers; ~10x faster on GPU vs scalar plain Swift, in fewer lines
- Heat distribution via Jacobi iteration — the four-neighbor stencil is a single `conv2d`; boundary conditions via `which` (elementwise ternary)
- Successive Over-Relaxation (SOR) — same kernel + `omega` overshoot; red/black checkerboard to emulate in-place updates; converges in ~N iterations vs ~N² for Jacobi (demo slowed 100x to be visible)
- Curve fitting — `grad` for automatic differentiation; gradient descent loop fits a quadratic with no hand-written derivatives
- Full toolkit — linear algebra, FFTs, N-d convolutions, reductions, scans, indexing, RNG, optimizers (SGD, Adam, RMSprop)
- Ecosystem — `mlx-swift`, `mlx-swift-lm`, `mlx-swift-examples`; MIT-licensed; one framework with four front-ends (Swift, Python, C++, C); prototype in Python, ship in Swift

## Related sessions to fetch (referenced in this talk)

- [ ] _No specific WWDC sessions were named in this talk._ (References are to repos and Python projects: `mlx-swift`, `mlx-swift-lm`, `mlx-swift-examples`, `mlx-lm`, `mlx-vlm`.)

## Chapter summary (Summary tab)

- **0:00 Introduction** — What numerical computing is and its applications: simulations (chemistry, biology, physics, finance), signal processing, rendering/ray tracing/fractals, and ML training via large-scale gradient descent.
- **0:57 MLX Swift and the Apple ecosystem** — Where MLX Swift sits vs Accelerate (CPU vector primitives), BNNS (neural-net building blocks), Metal Performance Shaders (GPU kernels), Swift Numerics (Complex + generic numeric protocols). Choose MLX Swift when the goal is math-like code with automatic GPU execution and automatic differentiation.
- **3:04 MLX Swift** — Core concepts: n-d arrays (NumPy-like), lazy evaluation building a compute graph before executing, and a power-iteration walkthrough where `matmul`, `norm`, `.T` map directly to notation.
- **4:28 Mandelbrot** — Fractal as an array-computing showcase: a scalar-at-a-time plain Swift version vs an MLX version applying `z = z² + c` across the whole grid on the GPU, up to 10x faster in fewer lines.
- **6:34 Heat distribution** — Steady-state temperature via Jacobi iteration: a 2D grid, the four-neighbor stencil as one `conv2d` call, boundary conditions as an elementwise ternary. A convolution as physics.
- **8:12 Faster convergence with SOR** — Successive Over-Relaxation reaches steady state in ~N iterations vs ~N² for Jacobi, using an `omega` overshoot and a red/black checkerboard to emulate in-place updates; ~100x faster with the same minimal code.
- **10:17 Curve fitting** — Automatic differentiation flips forward computation: given data points and a quadratic, `grad` derives gradients automatically and a gradient-descent loop fits the curve with no hand-written derivatives.
- **12:17 The full MLX toolkit and ecosystem** — Linear algebra, FFTs, n-d convolutions, reductions, scans, indexing, RNG, optimizers (SGD, Adam, RMSprop); Swift packages `mlx-swift`, `mlx-swift-lm`, `mlx-swift-examples`.
- **13:47 Next steps** — Getting started with `mlx-swift` and `mlx-swift-examples` (LLM integration, stable diffusion, training/fine-tuning, session examples); MLX's multi-language front-ends (Swift, Python, C++, C).

## Code

See `code.md` — 7 snippets extracted from the Code tab.
