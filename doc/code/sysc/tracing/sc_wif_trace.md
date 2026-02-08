# SystemC Tracing: WIF Trace (`sc_wif_trace`)

> **Source**: `ref/systemc/src/sysc/tracing/sc_wif_trace.cpp`

## 1. Overview
This file implements Waveform Intermediate Format (WIF) tracing. It mirrors the structure of the VCD implementation but outputs WIF syntax.

## 2. Architecture

### 2.1 `wif_trace` Class Hierarchy
-   Similar to VCD, has a base `wif_trace` and specific derived classes (`wif_bool_trace`, `wif_sc_logic_trace`, etc.).
-   **WIF Specifics**: Uses WIF syntax for assignments, e.g., `assign <name> <value> ;`.

### 2.2 `wif_trace_file` Class
-   **Container**: Manages the list of traces.
-   **Header Generation**: Writes WIF properties like `type scalar "BIT" enum ...`.

## 3. Key Differences from VCD
-   **Format**: Uses `assign` statements instead of VCD's value-change syntax.
-   **Delta Time**: Has explicit support for `delta_time` directive, allowing it to represent delta cycles more explicitly than standard VCD.
-   **Unsupported Intefaces**: Explicitly reports errors for `sc_event` and `sc_time` tracing (unlike VCD which supports them).

## 4. Execution Flow
1.  **Initialize**: `do_initialize` writes definitions.
2.  **Cycle**: `cycle()` checks for changes.
    -   Calculates `delta_units` if stepping within the same time step.
    -   Iterates traces, calls `write()` for changed ones.
