# Top-Down Analysis: Communication & Signals

SystemC separates **computation** (processes) from **communication** (channels). This separation allows modules to be designed independently of the protocols used to connect them.

## 1. The Interface-Method Call (IMC) Pattern
At its heart, SystemC communication is just C++ method calls.
-   **Interface (`sc_interface`)**: Defines the *contract* (pure virtual functions). E.g., `write()`, `read()`.
-   **Channel (`sc_prim_channel` / hierarchical channel)**: Implements the interface. E.g., `sc_signal` implements `write` by storing a value.
-   **Port (`sc_port`)**: Holds a pointer to the interface.

**Execution Flow**:
1.  **Process**: Calls `port->write(10)`.
2.  **Port**: Dereferences its internal interface pointer: `interface->write(10)`.
3.  **Channel**: Executes the `write` logic (e.g., updates the value).

## 2. Primitive Channels & Determinism
Primitive channels (like `sc_signal`, `sc_fifo`, `sc_mutex`) implement the `sc_prim_channel` base class. They use the **Request-Update** pattern to ensure simulation determinism (similar to Verilog's NBA).

### 2.1 The Request Phase (Evaluate)
When a process calls `signal.write(new_val)`:
-   The signal checks if `new_val != current_val`.
-   If different, it stores `new_val` in a "next value" buffer.
-   It calls `request_update()`, which registers the channel with the `sc_prim_channel_registry`.
-   **Crucially**: The `current_val` remains unchanged during this delta cycle!

### 2.2 The Update Phase
After all processes have run (Evaluation phase ends):
-   The kernel iterates through the registry and calls `update()` on all registered channels.
-   `sc_signal::update()`: Copies "next value" to "current value".
-   If the value changed, it creates a **Delta Notification** event.

### 2.3 The Notification Phase
-   The kernel triggers the delta notification events.
-   Any processes sensitive to `signal` (e.g., `sensitive << signal`) are made runnable for the *next* delta cycle.

## 3. Port Binding resolution
Binding ports is the act of connecting the `port` in a module to a generic `interface` (which could be a channel or another port).
-   **Elaboration Time**: Usage of `port(channel)` or `port.bind(channel)` builds a list of connections.
-   **Resolution**: At `end_of_elaboration`, SystemC "flattens" the connections. Even if you have `Port A -> Port B -> Port C -> Channel`, after resolution, `Port A` will point *directly* to `Channel`. This ensures that during simulation, communication is just a single pointer dereference (zero overhead).

## 4. Hardware Mapping
| SystemC Concept | Verilog Equivalent |
| :--- | :--- |
| `sc_signal` | `wire` or `reg` |
| `sc_in`, `sc_out` | `input` / `output` ports |
| `write()` | Assigment (`=`, `<=`) |
| `sc_fifo`, `sc_mutex` | No direct primitive (requires explicit behavioral code) |
