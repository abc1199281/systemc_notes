# TLM-2.0 Core: Global Quantum (`tlm_global_quantum`)

> **Source**: `ref/systemc/src/tlm_core/tlm_2/tlm_quantum/tlm_global_quantum.cpp`

## 1. Overview
`tlm_global_quantum` is a singleton class that maintains the global simulation quantum for temporally decoupled initiators. The quantum defines the maximum time an initiator can run ahead of the SystemC kernel time.

## 2. Key Methods
-   **`instance()`**: Returns the singleton instance.
-   **`compute_local_quantum()`**: Calculates the remaining local quantum for a process.
    -   Formula: `GlobalQuantum - (CurrentTime % GlobalQuantum)`
    -   This effectively aligns the local quantum boundaries to integer multiples of the global quantum.

## 3. Usage
Initiators check `compute_local_quantum()` or simply access the global quantum value to decide when they must sync (yield) to the SystemC kernel via `wait()`.
