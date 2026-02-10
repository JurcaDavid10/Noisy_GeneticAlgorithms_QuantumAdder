# Noisy Quantum Approximate Adder (XOR) — Genetic Algorithm Optimisation (Qiskit Aer)

This project explores **hardware-aware optimisation** of a small **approximate quantum “adder”** circuit (here: the **XOR** of two 1-bit inputs) under a **realistic noise model** using **Qiskit Aer**.  
A **Genetic Algorithm (GA)** evolves a compact 3-qubit circuit to **maximize success probability** while also **penalizing circuit complexity** (CNOT count + depth).

---

## What this code does

For each input pair **(x, y) ∈ {(0,0), (0,1), (1,0), (1,1)}**, the script:

1. Prepares the input state `|x, y, 0⟩` on 3 qubits (qubits 0,1 are inputs; qubit 2 is output).
2. Applies an **approximate adder** circuit parameterized by a **genome**:
   - **4 continuous parameters**: rotation angles `(theta1..theta4)` for `RY` gates on the output qubit.
   - **3 binary structure genes**: `(b1, b2, b3)` that switch optional `CX` gates on/off.
3. Measures **qubit 2** and checks if it matches the XOR target `z = (x + y) % 2`.
4. Runs the circuit on **AerSimulator with noise** and estimates success probability from repeated shots.

The GA searches for genomes that minimize the objective:

\[
\text{cost} = (1 - \text{avg\_success}) + \lambda \cdot \text{avg\_CX} + \mu \cdot \text{avg\_depth}
\]

Where:
- `avg_success` = mean XOR success probability over all 4 inputs  
- `avg_CX` = mean number of CNOT gates (after transpilation)  
- `avg_depth` = mean transpiled circuit depth  
- `lam_cx` and `mu_depth` control the penalty strength (defaults: `lam_cx=0.02`, `mu_depth=0.001`)

---

## Noise model

The simulator uses a simple, configurable **Aer noise model**:

- **Depolarizing noise** on 1-qubit gates (`rx`, `ry`, `rz`) with probability `p1` (default `0.001`)
- **Depolarizing noise** on 2-qubit `cx` gates with probability `p2` (default `0.01`)
- **Readout error** that flips measured bits with probability `p_ro` (default `0.02`)

You can adjust these in `make_noise_model(p1, p2, p_ro)`.

---

## Two optimisation modes

The script runs **two experiments**:

### 1) Evolved structure (bits evolve)
- GA evolves both angles **and** structure bits `(b1, b2, b3)`
- This allows the GA to discover a good trade-off between accuracy and fewer entangling gates.

### 2) Fixed structure (bits fixed to 110)
- Structure bits are locked to a chosen pattern (default `(1,1,0)`)
- GA evolves **angles only**, enabling a fair comparison against a fixed design.

At the end, it prints a summary comparing:
- average success
- average CNOT count
- average transpiled depth

---

## Requirements

- Python 3.x
- `numpy`
- `matplotlib`
- `qiskit`
- `qiskit-aer`

Install:
```bash
!pip -q install qiskit qiskit-aer
