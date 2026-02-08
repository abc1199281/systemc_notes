# SystemC Module Registry Analysis

> **File**: `ref/systemc/src/sysc/kernel/sc_module_registry.cpp`

## 1. Overview
The `sc_module_registry` is a specialized container used by the simulation context to keep track of **all** modules instantiated in the system. It is primarily for internal use to drive simulation phases like elaboration and simulation callbacks.

## 2. Core Mechanism

### 2.1 Registration
- **`insert(sc_module&)`**: Called by `sc_module::sc_module_init()`. It adds the module to `m_module_vec`.
- **Checks**: It enforces that you cannot insert modules after elaboration is done or while simulation is running.

### 2.2 Phase Callbacks
The registry acts as a broadcaster, iterating over all moduels to call their lifecycle methods:
- `construction_done()`
- `elaboration_done()`
- `start_simulation()`
- `simulation_done()`

## 3. Key Takeaways
1.  **Global List**: While the `sc_object_manager` maintains the *hierarchy* (tree), the `sc_module_registry` maintains a *flat list* of all modules.
2.  **Snapshot**: It allows the kernel to iterate over all modules efficiently without traversing the object tree.
3.  **Debug**: In debug mode (`DEBUG_SYSTEMC`), it checks for duplicate insertions.
