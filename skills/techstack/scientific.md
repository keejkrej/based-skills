# Scientific

Imaging, numerics, simulation, and analysis.

## C++ scientific imaging

- Use CMake for builds
- Use Qt 6 for app UI
- Use VTK for 3D visualization and data pipelines
- Use ITK for n-dimensional image IO, filtering, registration, and algorithms
- Build tooling (Ninja, vcpkg, generators) → [dev.md](dev.md)
- Native Qt desktop apps → [desktop.md](desktop.md)

## Rust numerics

- Use `foo.rs` plus `foo/` child modules; never use `mod.rs`
- Prefer established scientific stacks over hand-rolled numerics
- Default to the `ndarray` ecosystem for NumPy-style n-D arrays; use `polars` for tabular/query workloads and `nalgebra` for small fixed-size geometry/LA
- Treat Python prototypes as transitional; keep core algorithms easy to migrate toward Rust/C++
- Production hardening → [backend.md](backend.md)

### ndarray core

- Use `ndarray` for n-D arrays, views, slicing, elementwise ops, `Zip`/`azip!`, `meshgrid`, `stack`/`concatenate`
- Enable `rayon` feature (or `par_azip!`) for parallel elementwise/reduction work — not hand-rolled thread pools over slices
- Enable `blas` feature when large dense matmul dominates — not naive O(n³) multiply loops

### ndarray ecosystem

- `ndarray-linalg` — SVD, eig, solve, inverse; not custom Gaussian elimination or power iteration
- `ndarray-stats` — quantiles, corr, entropy, histograms; not rolling-window or covariance loops
- `ndarray-rand` — random arrays and sampling; not manual RNG index loops
- `ndarray-npy` — `.npy` / `.npz` I/O; not ad-hoc binary layout parsers
- `ndarray-csv` — homogeneous CSV ↔ `Array2`; not manual parse-and-reshape
- `ndarray-einsum` — Einstein summation; not nested index loops for tensor contractions
- `nshare` — interop with `nalgebra` / `image`; not manual buffer copies between types

### Other stacks (when ndarray is the wrong fit)

- `nalgebra` — 1D/2D column-major LA, transforms, compile-time shape checks
- `polars` — DataFrames, joins, groupby, lazy queries (pandas-like)
- `statrs` — standalone distribution objects, CDFs, sampling (pair with `ndarray-stats` for array-native stats)
- `argmin` — general optimization (gradient descent, line search)
- `rustfft` — FFT and frequency-domain pipelines
- `linfa` — classical ML (clustering, regression, etc.)

### Anti-patterns

- Manual rolling mean, variance, or covariance over slices
- Custom matrix decompositions or linear solvers
- Reimplementing NumPy/scipy routines that exist in the crates above
- Raw `Vec<Vec<f64>>` as a stand-in for `Array2` in numerical code

## Docs

- CMake: https://cmake.org/cmake/help/latest/
- VTK: https://vtk.org/documentation/
- ITK: https://docs.itk.org/
- ndarray: https://docs.rs/ndarray
- ndarray for NumPy users: https://docs.rs/ndarray/latest/ndarray/doc/ndarray_for_numpy_users/index.html
- ndarray-linalg: https://docs.rs/ndarray-linalg
- ndarray-stats: https://docs.rs/ndarray-stats
- polars: https://docs.polars.rs
