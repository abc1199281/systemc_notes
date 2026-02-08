# SystemC Export Analysis

> **File**: `ref/systemc/src/sysc/communication/sc_export.cpp`

## 1. Overview
`sc_export` allows a module to **provide** an interface to the outside (e.g., exposing an internal channel or `this` module as an implementation of an interface).

## 2. Similarity to Ports
- It is technically very similar to `sc_port`.
- It has a registry (`sc_export_registry`).
- It can be bound.

## 3. Differences
- **Direction**: A port "uses" an interface. An export "provides" it.
- **Binding**: When you bind `port.bind(export)`, the port gets a pointer to the interface that the export holds.
- **Hierarchy**: Exports allow "upward" exposure of detailed internal implementations to higher levels of abstraction.

## 4. Lifecycle
Like ports, exports have lifecycle callbacks (`before_end_of_elaboration`, `start_of_simulation`, etc.), managed by the registry.

## 5. Key Takeaways
1.  **Structural Connectivity**: Exports are crucial for hierarchical design where a parent module wraps a channel and exposes it to siblings via an export.
