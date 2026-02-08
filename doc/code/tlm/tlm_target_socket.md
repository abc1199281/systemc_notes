# TLM Target Socket Analysis

> **File**: `ref/systemc/src/tlm_core/tlm_2/tlm_sockets/tlm_target_socket.h`

## 1. Overview
The `tlm_target_socket` is the complement to the initiator socket, designed to be exposed by target modules (slaves/peripherals).

## 2. Structure
It wraps:
- **`sc_export`**: For the **Forward Path** (Receiving calls from the initiator).
- **`sc_port`**: For the **Backward Path** (Sending calls back to the initiator).

## 3. Binding
Standard target socket binding accepts an `initiator_socket`:
```cpp
target_socket.bind( initiator_socket ); // Less common, usually initiator binds to target
```
Or allows hierarchical binding (target socket of a parent module bound to target socket of a child module).

## 4. Key Takeaways
1.  **Symmetry**: The structure mirrors the initiator socket, maintaining the `port/export` duality required for SystemC's binding mechanism to work.
2.  **Versioning**: TLM 2.0 sockets are heavily templated (`BUSWIDTH`, `TYPES`, `N`, `POL`) to support various bus widths and protocol types, though defaults cover 90% of use cases.
