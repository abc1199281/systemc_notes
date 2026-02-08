# SystemC Utils: Stop Here (`sc_stop_here`)

> **Source**: `ref/systemc/src/sysc/utils/sc_stop_here.cpp`

## 1. Overview
A tiny utility file designed primarily to assist with **debugging**. It provides functions that are called when `SC_STOP` or `SC_INTERRUPT` actions are triggered by the report handler.

## 2. Functions
-   **`sc_stop_here(id, severity)`**: Called before `sc_stop()`.
-   **`sc_interrupt_here(id, severity)`**: Called when `SC_INTERRUPT` is requested.

## 3. Usage Pattern
Developers allow to set breakpoints on these functions in their debugger (GDB/Visual Studio).
-   Inside the function, a `switch(severity)` assigns the `id` to static pointers (`info_id`, `warning_id`, etc.). This code effectively does "nothing" at runtime but provides a perfect place to break execution to inspect the stack *before* the simulation stops or throws.
