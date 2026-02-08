# SystemC Sensitivity Analysis

> **File**: `ref/systemc/src/sysc/kernel/sc_sensitive.cpp`

## 1. Overview
Handles the **static sensitivity** syntax used in module constructors (e.g., `sensitive << clk.pos();`). It allows processes to declare what events trigger them *before* simulation starts.

## 2. The `sc_sensitive` Object
Every `sc_module` has members `sensitive`, `sensitive_pos`, and `sensitive_neg`.
- These are objects of type `sc_sensitive`, `sc_sensitive_pos`, etc.
- They maintain a **current mode** (`SC_METHOD`, `SC_THREAD`) and a **current handle** (the process being created).

### 2.1 Operator `<<`
The magic happens in `operator<<`.
1.  **Switching Context**: `sensitive << process_handle` switches the `m_handle` member to point to that process.
2.  **Adding Sensitivity**: `sensitive << event` (or port/interface) calls `m_handle->add_static_event(e)`.

## 3. Variants
- **`sc_sensitive`**: Generic events (value change).
- **`sc_sensitive_pos`**: Positive edge events (`posedge_event()`).
- **`sc_sensitive_neg`**: Negative edge events (`negedge_event()`).

## 4. Deprecation
The file handles legacy syntax (function call operator `()`) by warning and delegating to `operator<<`.

## 5. Key Takeaways
1.  **Construction Time Only**: Static sensitivity is defined during construction/elaboration. Once simulation starts (`sc_is_running()`), attempting to use these operators triggers an error.
2.  **Event Finders**: For ports, it often uses `sc_event_finder` to defer the actual event lookup until the port is bound (similar to the reset finder logic).
