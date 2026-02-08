# SystemC Port Analysis

> **File**: `ref/systemc/src/sysc/communication/sc_port.cpp`

## 1. Overview
`sc_port` is the mechanism through which modules communicate with channels (outside world). It "requires" an interface.

## 2. Binding Mechanism
Binding is the process of connecting a port to an interface (channel) or another port.

### 2.1 `sc_bind_info`
Stores the binding status.
- `vec`: List of bound elements.
- `has_parent`: True if this port is bound to a parent port (hierarchical binding).

### 2.2 `bind()`
- **bind(interface)**: Adds the interface to the bind list.
- **bind(parent_port)**: Links this port to the parent port. The parent port must eventually be bound to an interface.

### 2.3 `complete_binding()`
The most critical function. It runs at the end of elaboration.
1.  **Flattening**: It walks up the hierarchy (`first_parent()`) to find the ultimate source interface. it "pulls down" the interface pointer from the parent port to the leaf port.
2.  **Registration**: Calls `interface->register_port(*this)` so the channel knows about the connection.
3.  **Sensitivity**: Resolves any static sensitivity (`sensitive << port`) by looking up the event finder on the now-bound interface.

## 3. Registry (`sc_port_registry`)
Tracks all ports in the simulation to trigger lifecycle callbacks (`elaboration_done`, `start_of_simulation`, etc.) on them.

## 4. Key Takeaways
1.  **Resolution**: Ports effectively "disappear" at simulation time; they become direct pointers to the channel's interface. The `complete_binding` phase ensures this efficient direct access.
2.  **Multiports**: The code handles `max_size` and policies (`SC_ONE_OR_MORE_BOUND`, etc.) to support ports connected to multiple channels.
