# SystemC Data Types: sc_logic

> **Source**: `ref/systemc/src/sysc/datatypes/bit/sc_logic.cpp`

## 1. Overview
`sc_logic` represents a single bit with **4-valued logic**, fundamental for hardware modeling where signal states include unknown (`X`) and high-impedance (`Z`).

## 2. The 4 Values
The values are defined in `sc_logic_value_t`:
-   `Log_0` ('0'): Logic Low
-   `Log_1` ('1'): Logic High
-   `Log_Z` ('Z'): High Impedance (floating)
-   `Log_X` ('X'): Unknown / Contention

## 3. Implementation Details

### 3.1 Lookup Tables
Logic operations are implemented using static 2D arrays (lookup tables) for efficiency. This ensures correct propagation of `X` and `Z` states.

-   **AND Table (`and_table`)**:
    -   `0 & X` -> `0` (Dominant 0)
    -   `1 & X` -> `X`
-   **OR Table (`or_table`)**:
    -   `1 | X` -> `1` (Dominant 1)
    -   `0 | X` -> `X`
-   **XOR Table (`xor_table`)**
-   **NOT Table (`not_table`)**

### 3.2 Constants
Global constants are provided for convenience:
-   `SC_LOGIC_0`, `SC_LOGIC_1`, `SC_LOGIC_Z`, `SC_LOGIC_X`

## 4. Usage
`sc_logic` is the building block for `sc_lv` (logic vector) and is used in resolved signals (`sc_signal_resolved`) to model bus contention (e.g., multiple drivers driving `Z` and one driving `1` results in `1`; multiple driving `1` and `0` results in `X`).
