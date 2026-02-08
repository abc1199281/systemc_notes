# SystemC Utils: Vector (`sc_vector`)

> **Source**: `ref/systemc/src/sysc/utils/sc_vector.cpp`

## 1. Overview
`sc_vector` is a specialized container for `sc_object`s (like modules, ports, channels). It solves the problem of creating and naming arrays of SystemC objects, which `std::vector` cannot do automatically (SystemC objects need unique string names at construction).

## 2. Features
-   **Naming**: Automatically generates hierarchical names for elements (e.g., `my_vec_0`, `my_vec_1`).
-   **Binding**: Offers `bind()` utilities to connect a vector of ports to a vector of signals/interfaces in one go.
-   **Initialization**: `init(n)` method creates `n` elements. It ensures this is done during elaboration (before simulation starts).
-   **Locking**: The vector becomes "locked" (cannot resize) after elaboration to ensure a stable simulation structure.

## 3. Implementation Details
-   **`sc_vector_base`**: Non-templated base class holding the core logic.
-   **`objs_vec_`**: An internal `std::vector<sc_object*>` cache used to return the elements generically via `get_elements()`.
-   **`report_empty_bind`**: specific warnings if user tries to bind empty vectors.

## 4. Usage
```cpp
sc_vector<sc_in<bool>> input_ports{"inputs", 4}; // Creates inputs_0, inputs_1...
sc_vector<sc_signal<bool>> signals{"sigs", 4};
input_ports.bind(signals); // Binds index-wise
```
