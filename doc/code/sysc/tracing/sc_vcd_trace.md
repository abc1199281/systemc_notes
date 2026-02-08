# SystemC Tracing: VCD Trace (`sc_vcd_trace`)

> **Source**: `ref/systemc/src/sysc/tracing/sc_vcd_trace.cpp`

## 1. Overview
This file implements Value Change Dump (VCD) tracing. It defines the `vcd_trace_file` class (derived from `sc_trace_file_base`) and a family of `vcd_trace` classes for handling different data types.

## 2. Architecture

### 2.1 `vcd_trace` Class Hierarchy
-   **Base**: `vcd_trace` (abstract base).
-   **Derived**: `vcd_bool_trace`, `vcd_sc_logic_trace`, `vcd_sc_int_base_trace`, etc.
-   **Role**: Each instance watches a specific variable/signal. It stores the `old_value` to detect changes (`changed()` method) and knows how to format the output for VCD (`write()` method).

### 2.2 `vcd_trace_file` Class
-   **Container**: Mantains a vector of `vcd_trace*` called `traces`.
-   **Scopes**: Uses `vcd_scope` helper class to generate the hierarchical variable definitions (`$scope`, `$upscope`, `$var`) in the VCD header.
-   **Cycle Execution**: The `cycle()` method is called by the base class at specific stages.
    -   It iterates through all traces.
    -   If `t->changed()` is true, it calls `t->write(fp)`.
    -   Manages VCD timestamps (`#1000`).

## 3. Key Implementations

### 3.1 Initialization (`do_initialize`)
-   Writes VCD header: `$date`, `$version`, `$timescale`.
-   Writes variable definitions (`$var`).
-   Dumps initial values (`$dumpvars`).

### 3.2 Delta Cycles
-   Supports tracing delta cycles if enabled.
-   If multiple delta cycles occur at the same simulation time, it may print them effectively as zero-delay updates, or warn about limitations if strict VCD compliance makes this difficult to visualize (VCD usually requires time advancement).

## 4. Supported Types
-   Integers (standard & SystemC arbitrary precision).
-   Logic/Bit vectors (`sc_bv`, `sc_lv`).
-   Floats/Doubles (`real`).
-   `sc_time`, `sc_event`.
