# Noisy_GeneticAlgorithms_QuantumAdder
Genetic-algorithm optimization of a noisy Qiskit approximate 1-bit adder (XOR) circuit. Evolves both gate parameters (RY rotations) and optional CNOT structure under an Aer depolarizing + readout noise model, balancing accuracy with circuit complexity (CX count, transpiled depth), and compares evolved vs fixed architectures with progress plots.
