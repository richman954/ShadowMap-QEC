# SHADOWMAP : Evolutionary Co-Design for Quantum Error Correction

**SHADOWMAP** is a blazing-fast, CPU-parallelized utility-scale simulation engine for Quantum Error Correction (QEC). It bridges the gap between High-Performance Computing (HPC) and Quantum Processing Units (QPU) by using gradient-free evolutionary algorithms to dynamically optimize Minimum Weight Perfect Matching (MWPM) metric spaces.

Inspired by the architectural roadmap laid out in *"How to Build a Quantum Supercomputer"* (Mohseni, Martinis, et al., arXiv:2411.10406), SHADOWMAP treats the QPU as a black box. It allows classical software to organically "learn" and route around manufacturing defects on quantum chips using only raw syndrome data, without requiring prior analytic noise models.

##  Key Features & Performance

* **Extreme CPU Performance:** Achieves **200,000+ full 3D Spacetime decodes per second** on standard cloud CPUs. Bypasses Python bottlenecks by compiling exact planar code geometries directly into `PyMatching` C++ / `SciPy` sparse matrices.
* **Spatial Defect Discovery:** Successfully mapped "Dead Zones" (localized 16%-20% physical error strips) entirely blindly, reducing logical error rates by up to 50%.
* **Temporal Sensor Calibration:** Extends to 3D Spacetime graphs to discover and route around faulty measurement sensors (e.g., 15% temporal measurement error rates).
* **Beating the Analytic Limit:** By empirically learning finite-size boundary effects and path degeneracy (entropy), SHADOWMAP dynamically warps the MWPM metric space to match or outperform the standard infinite-lattice Maximum-Likelihood log-odds limit (McNemar $p < 10^{-15}$).

## The Physics Problem

Analytic MWPM decoders assign edge weights using the maximum-likelihood formula $w = \ln((1-p)/p)$. However, this formula assumes an infinite lattice and largely ignores path degeneracy. On real, finite-size quantum chips with severe manufacturing variations, this analytic assumption breaks down. 

SHADOWMAP uses a Darwinian evolutionary optimizer to tune the 2D and 3D metric spaces of the decoder, adjusting spatial and temporal weights to herd the C++ matcher away from logical failures caused by finite-size boundary distortions and defective hardware.

## Experimental Highlights

### Experiment 1: The "Dead Zone" (2D Spatial Mapping)
A simulated $d=11$ chip with a baseline 3% error rate, but two massive fabrication defects (12% at Col 2, and 20% at Col 8). The optimizer was given an 11-dimensional parameter space to explore.
* **Baseline Failure Rate:** 0.135%
* **SHADOWMAP Optimized Rate:** 0.065% (McNemar $p = 1.96 \times 10^{-9}$)
* **Result:** SHADOWMAP successfully mapped the defects, autonomously dropping the weights of Col 2 and 8 while maintaining healthy regions. It slashed the logical failure rate in half, perfectly matching the theoretical analytic limit blindly.

### Experiment 2: Broken Sensors (3D Spacetime Mapping)
A 3D Spacetime block ($d=7, T=7$) with 2% healthy noise, but a broken measurement sensor at Column 3 reporting 15% measurement errors. 
* **Baseline Failure Rate:** 27.92%
* **SHADOWMAP Optimized Rate:** 23.49%
* **Result:** McNemar $p = 1.21 \times 10^{-126}$. The engine realized the time-like edges at Column 3 were unreliable and autonomously crushed their weights (from ~3.89 down to 1.94) to route through time around the broken hardware.

### Experiment 3: The "Meteor Crater" (84-Dimensional Atomic Mapping)
We simulated a $d=7$ chip with a healthy 4% error rate, but injected a highly localized 3x3 "Meteor Crater" (18% error rate) dead in the center of the chip. We gave SHADOWMAP 84 independent continuous dimensions (every physical edge on the chip) and unleashed a Covariance Matrix Adaptation Evolution Strategy (CMA-ES).

Without any prior knowledge of the grid geometry, CMA-ES built an 84x84 covariance matrix and successfully "drew" a map of the damaged atoms, drastically dropping the MWPM weights in the exact center of the lattice to build topological walls around the defect.

* **Baseline Failure Rate:** 4.54%
* **SHADOWMAP CMA-ES Rate:** 3.45% (McNemar $p = 6.37 \times 10^{-62}$)

**SHADOWMAP's Blind ASCII Render of the Hardware:**
*(Visualizing the learned weights. Values < 2.3 are marked with `*` indicating discovered damage.)*
```text
HORIZONTAL EDGE WEIGHTS (Expected Crater: Rows 2-4, Cols 2-3):
Row 2:   3.6    3.4  * 1.9*   2.7    4.9    3.8 
Row 3:   3.6    3.7  * 1.8* * 1.2*   3.4    3.8 
Row 4:   4.2    4.4  * 2.0* * 1.9*   4.3    4.4 

VERTICAL EDGE WEIGHTS (Expected Crater: Rows 2-3, Cols 2-4):
Row 2:   2.4    3.3  * 1.2* * 1.0*   2.4    3.1    2.4 
Row 3: * 2.3*   3.6  * 1.4* * 1.3* * 1.5*   3.5    4.4

## Tech Stack
* `PyMatching v2.x` (C++ Sparse Matrix Compiler)
* `SciPy` (Sparse Matrix generation)
* `NumPy` (Vectorized CRN noise generation)
* `concurrent.futures` (Multi-core CPU parallelization)


## Feature Roadmap

Circuit-Level Noise (Stim Integration): Right now, we inject noise phenomenologically (we just randomly flip edges on a graph). In reality, quantum errors happen during the actual physical microwave pulses and gates (CNOTs, Hadamards). The next major step is integrating Google's Stim library (written by Craig Gidney) to simulate exact circuit-level gate failures, and letting Stim generate the routing matrices for PyMatching.

Fully Granular Defect Maps: Currently, SHADOWMAP optimizes entire columns of the chip at once. The next step is giving the AI the power to tune the weight of every single individual edge on the chip independently, mapping exact atomic-level defects.

Advanced AI Optimizers: We used a basic Darwinian genetic algorithm. To optimize a 1,000-dimensional continuous parameter space (every individual edge on a large chip), we would upgrade the AI engine to use CMA-ES (Covariance Matrix Adaptation) or Bayesian Optimization.
