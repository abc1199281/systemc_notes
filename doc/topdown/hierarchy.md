# Top-Down Analysis: Structural Hierarchy & Elaboration

SystemC models are structured hierarchically, similar to VHDL or Verilog, but constructed dynamically at runtime using C++.

## 1. Elaboration: The Build Phase
Unlike compiled HDLs, SystemC "compiles" into a C++ executable. The structure of the hardware (modules, ports, signals) is created during the **Elaboration Phase**, which runs from the start of `sc_main()` until `sc_start()` is called.

### 1.1 The Module (`sc_module`)
The `sc_module` is the container class.
-   **Registration**: When a module is instantiated, it registers itself with the `sc_simcontext`.
-   **Hierarchy Stack**: The `sc_object_manager` maintains a stack of "active" objects.
    1.  When `TopLevel` constructor starts, it pushes itself onto the stack.
    2.  When `SubModule` is instantiated inside `TopLevel`, `SubModule` sees `TopLevel` as its parent.
    3.  When `SubModule` constructor ends, it pops itself.
    4.  When `TopLevel` constructor ends, it pops itself.

### 1.2 The Name Dance (`sc_module_name`)
C++ constructors don't know the name of the variable being created. SystemC uses a clever trick with `sc_module_name`:
```cpp
SC_MODULE(my_mod) {
    SC_CTOR(my_mod) { ... }
};
// generic:
my_mod m1("instance_name");
```
-   The `sc_module_name("instance_name")` object is created **before** the `my_mod` constructor body.
-   It pushes the name onto a special stack in the kernel.
-   The `sc_module` constructor pops this name to assign it to the module instance.
-   This ensures every object has a string name for reporting and waveform tracing.

## 2. The Object Registry
The `sc_simcontext` owns an `sc_object_manager` which tracks:
-   **Object Hierarchy**: Parent-child relationships (Project -> Top -> Sub -> Signal).
-   **Search**: Functions like `sc_find_object("top.sub.sig")` traverse this registry.
-   **Uniqueness**: It enforces unique names within a scope, renaming duplicates (e.g., `sig`, `sig_0`, `sig_1`) if necessary to prevent collisions.

## 3. Stages of Simulation
SystemC defines precise stages (callbacks) for managing hierarchy:

1.  **Construction**: C++ constructors run. The basic tree is built.
2.  **`before_end_of_elaboration()`**: User callback. Last chance to instantiate modules or change attributes.
3.  **`end_of_elaboration()`**: User callback. Hierarchy is frozen.
    -   **Optimization**: Port binding is finalized.
    -   **Validation**: Unbound ports are checked.
4.  **`start_of_simulation()`**: User callback. Open files, print headers.
5.  **Simulation Loop**: `crunch()` runs.
6.  **`end_of_simulation()`**: User callback. Clean up.

## 4. Hardware Mapping
| SystemC Concept | Verilog Equivalent |
| :--- | :--- |
| `sc_module` | `module ... endmodule` |
| `new Module("name")` | `Module name();` (Instantiation) |
| `end_of_elaboration` | Time 0 initialization (conceptually) |
| `sc_main` | Top-level testbench file |
