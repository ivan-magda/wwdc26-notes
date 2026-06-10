---
title: "Explore numerical computing in Swift with MLX — Full Digest"
session: WWDC26 · 328
url: https://developer.apple.com/videos/play/wwdc2026/328/
duration: 14m
speakers: David Koski
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 328 — Explore numerical computing in Swift with MLX

## TL;DR

A 14-minute, example-driven introduction to **MLX Swift** for numerical computing —
the case being: write code that *looks like the math*, and get GPU execution and
automatic differentiation for free.

1. **Where it fits.** Apple already has Accelerate (CPU vector primitives), BNNS (NN
   building blocks), Metal Performance Shaders (raw GPU kernels), and Swift Numerics
   (`Complex` + generic numeric protocols). Reach for **MLX Swift** when your primary
   goal is mathematical code with an eye for performance and you don't want the
   bookkeeping of scalar loops or the overhead of lower-level libraries.
2. **The core abstraction** is the n-dimensional array (NumPy-like — most NumPy
   translates with minimal changes). Operations are **lazy**: they build a compute
   graph and run only on `eval` or when a value is read.
3. **Two payoffs of array computing + lazy eval:** automatic GPU execution (GPU by
   default) and automatic differentiation (`grad`).
4. **Three worked examples** drive it home: **Mandelbrot** (embarrassingly parallel,
   ~10x over scalar Swift), **heat distribution** (neighbor-coupled — a stencil is one
   `conv2d`; Jacobi then SOR for ~N vs ~N² convergence), and **curve fitting**
   (gradient descent with `grad`, no hand-written derivatives).
5. **It's an ecosystem.** MIT-licensed, one framework with four front-ends (Swift,
   Python, C++, C); `mlx-swift`, `mlx-swift-lm`, `mlx-swift-examples`. Prototype in
   Python, ship in Swift.

---

## 1. Where MLX Swift fits (and the core idea)

Apple's numerical stack already has strong, specialized pieces:

- **Accelerate** — hand-tuned vector primitives on the CPU.
- **BNNS** — the building-block layer for neural networks.
- **Metal Performance Shaders** — direct access to GPU kernels.
- **Swift Numerics** — a `Complex` type and generic numeric protocols.

You choose **MLX Swift** when the primary goal is *writing mathematical code with an
eye for performance* — the code reads like the math, without the overhead of
lower-level libraries or the array bookkeeping of plain Swift.

The mental model: mathematicians work with **vectors and matrices**, operating on the
whole matrix at once rather than single values. MLX Swift's central abstraction is the
**n-dimensional array** (like NumPy), and two properties — **array computing** and
**lazy evaluation** — are what enable **automatic GPU execution** and **automatic
differentiation**. The whole MLX ecosystem is **open source under MIT**.

### Power iteration — the operations map to notation

The intro example finds the top eigenvector/eigenvalue of a symmetric matrix:

```swift
import MLX
let n = 100
let steps = 10
let B = MLXRandom.normal([n, n])
var v = MLXRandom.normal([n])

// get symmetric matrix A = Bᵀ + B
let A = B.T + B

// Power iteration → top eigenvector of A.
//   v ← A v / ‖A v‖
for _ in 0 ..< steps {
    let Av = matmul(A, v)
    v = Av / norm(Av)
    eval(v)
}

// recover the eigenvalue.
//   λ = vᵀ A v
let lambda = matmul(matmul(v.T, A), v)

print(lambda)
```

- `.T` is transpose, `+` is matrix addition, `matmul` is matrix-vector multiply, `norm`
  is the L2 norm — each line reads like the math.
- **Lazy evaluation in action:** operations on `MLXArray` objects build a compute graph;
  nothing runs until you call `eval` or read a value. Inside the loop, `eval(v)` is
  called each step **so the graph stays small** rather than growing across iterations.
- Reading `lambda` (here via `print`) forces the final computation.
- If you actually need *all* eigenvalues/eigenvectors, the MLX Swift **linear algebra
  package** has functions for that.

## 2. Mandelbrot — array computing showcase

For each point `c` in the complex plane, iterate `z = z² + c`; if the magnitude never
exceeds 2 the point is in the set (black), otherwise it's colored by how fast it
escapes. It's *embarrassingly parallel* — every point is independent.

**Plain Swift, scalar-at-a-time** — idiomatic, but loops over every pixel on the CPU and
carries bookkeeping unrelated to the problem:

```swift
// Plain Swift, scalar-at-a-time
var counts = Array2D<Int>(width: w, height: h)

for y in 0 ..< h {
    for x in 0 ..< w {
        let c = Complex(xMin + Float(x) * xStep, yMin + Float(y) * yStep)
        var z = Complex<Float>.zero
        var limit = maxIterations
        for i in 0 ..< maxIterations {
            z = z * z + c
            if z.lengthSquared > radiusSquared {
                limit = i
                break
            }
        }
        counts[x, y] = limit
    }
}
```

**MLX Swift** — build the grid of complex numbers once, then iterate over the whole grid
in two lines, on the GPU by default:

```swift
// Compute the Mandelbrot set on a grid of complex numbers
import MLX

let x = linspace(-2.0, 0.5, count: w)
let y = linspace(-1.25, 1.25, count: h).reshaped(h, 1)
let c = x + y.asImaginary()

var z = MLXArray.zeros(like: c)
var counts = MLXArray.zeros(c.shape, dtype: .int16)

for _ in 0 ..< maxIterations {
    z = z * z + c                       // iterate z ← z² + c
    counts = counts + (abs(z) .< 2)     // count bounded iterations
}
```

The takeaway David draws: plain Swift is expressive but scalar-at-a-time, and the
bookkeeping can obscure the math; MLX operates on arrays, reads like the math, runs in
parallel on the GPU, and **10x faster is certainly possible** — with smaller, simpler
code.

## 3. Heat distribution — a convolution as physics

Unlike Mandelbrot, here **each cell talks to its neighbors** — a pattern that shows up in
physics, image processing, and neural networks. MLX handles it with a single operation:
**convolution**.

The problem: a room with walls and heat sources; find the steady-state temperature
everywhere. The **Jacobi iteration** models temperature as a 2D grid and, each step,
averages the four neighbors with a fixed stencil — which is exactly a convolution.

```swift
// Jacobi iteration: average the four neighbors

// Convolution weights
let kernel = MLXArray(converting: [
    0,    0.25, 0,
    0.25, 0,    0.25,
    0,    0.25, 0,
]).reshaped(1, 3, 3, 1)

// Initial value
var temperature = heatSources

// Run this in a loop until convergence
let next = conv2d(temperature, kernel, padding: 1)
temperature = which(heatMask, heatSources, next)
```

Two lines do the work: `conv2d` applies the stencil across the entire grid (the
*physics*), and `which` — an **elementwise ternary** — applies the boundary conditions:
where the mask marks a heat source or wall, keep the fixed value; everywhere else take
the convolution result.

### Faster convergence with SOR

Jacobi is cheap per step but slow to converge — heat moves one cell at a time, so steady
state typically needs **~N² iterations** (N = grid side). **Successive Over-Relaxation
(SOR)** uses the *same kernel* plus a parameter **omega** that overshoots each update in
the direction of change (the overshoot recovers as it iterates). With the optimal omega
it converges in **~N iterations**.

The second ingredient is **in-place updates**. MLX usually produces new arrays rather
than mutating in place, so SOR uses a **red/black checkerboard**: update red cells, then
update black cells using the now-fresh red neighbors — giving the in-place effect.

```swift
// Successive Over-Relaxation: blend the previous and next state
let ω: Float = 2.0 / (1.0 + sin(Float.pi / Float(max(M, N))))

let redMask   = checkerboard(rows: M, cols: N, phase: 0)
let blackMask = checkerboard(rows: M, cols: N, phase: 1)

// Update red cells using black neighbors
let sorRed  = ω * conv2d(temperature, kernel, padding: 1) + (1 - ω) * temperature
temperature = which(redMask, sorRed, temperature)
temperature = which(heatMask, heatSources, temperature)

// Update black cells using (now-updated) red neighbors
let sorBlack = ω * conv2d(temperature, kernel, padding: 1) + (1 - ω) * temperature
temperature  = which(blackMask, sorBlack, temperature)
temperature  = which(heatMask, heatSources, temperature)
```

The Jacobi and SOR code are nearly identical and both closely match the math. In the
side-by-side animation, Jacobi spreads slowly while SOR fills the area fast with a
striking ripple (the overshoot-and-correct in real time), both converging to the same
configuration. David notes he had to **slow SOR down 100x just to make it visible** —
"the power of choosing the right algorithm."

## 4. Curve fitting — automatic differentiation

The first two examples were *forward* computation (inputs → outputs). Curve fitting flips
it: you have outputs (data points) and want the parameters that produce them. The key
transformation is **`grad`** for automatic differentiation.

Pick a function structure (here a **quadratic** → a parabola), define a **mean-squared-
error** loss, and minimize it with gradient descent — the same core idea as training any
ML model, at small scale.

```swift
// Define a loss, then optimize it with autodiff
// x, y: data points as MLXArrays
func f(_ θ: MLXArray) -> MLXArray {
    θ[0] + θ[1] * x + θ[2] * x ** 2
}

func loss(_ θ: MLXArray) -> MLXArray {
    mean((f(θ) - y) ** 2)
}

var θ = zeros([numParams])
let gradLoss = grad(loss)

for _ in 0 ..< steps {
    let g = gradLoss(θ)         // ∇L(θ)
    θ = θ - learningRate * g    // parameter update
    eval(θ)                     // force evaluation
}
```

`grad(loss)` turns the loss into a function that returns the **exact gradient** with
respect to the parameters — **no derivatives written by hand**. The loop evaluates the
gradient, takes a small step, and calls `eval` each iteration to flush the graph so it
doesn't grow unbounded. The parabola overshoots, then settles into closer and closer
fits.

David notes a quadratic could have been fit directly with **QR** from the linear-algebra
package — the point is that **gradients work with arbitrarily complex functions**. And
when you need more than raw gradients, MLX ships optimizers: **SGD, Adam, RMSprop, and
more**.

## 5. The full toolkit and ecosystem

The session showed array computing, convolution, and `grad`, but MLX includes the full
numerical-computing toolkit: **linear algebra, FFTs, N-dimensional convolutions,
reductions, scans, indexing, random number generation**, and more.

Swift packages, all open source and SPM-installable:

- **`mlx-swift`** — the core framework shown throughout (has docs and tests to explore).
- **`mlx-swift-lm`** — Swift language-model implementations.
- **`mlx-swift-examples`** — example programs (LLM integration, stable diffusion, model
  training/fine-tuning) — *examples based on this session will be posted there*.

MLX is **one framework with four front-ends** — Swift, Python, C++, C — sharing the same
concepts, operations, and lazy-evaluation model, so patterns transfer with minimal
changes. Practical workflow: **prototype in Python, ship in Swift**. The Python side has
a broader research ecosystem (`mlx-lm`, `mlx-vlm` worth a look). Contributions welcome —
open issues to fix or new example programs to add.
