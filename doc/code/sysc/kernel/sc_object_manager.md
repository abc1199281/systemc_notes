# SystemC Object Manager Analysis

> **File**: `ref/systemc/src/sysc/kernel/sc_object_manager.cpp`

## 1. Overview
`sc_object_manager` is the central database of all simulation objects. It maintains the:
- **Instance Table**: An `active` registry of all named objects.
- **Object Hierarchy Stack**: Tracks the "current" parent during object construction.
- **Module Name Stack**: Tracks `sc_module_name` objects for correct module construction.

## 2. Core Responsibilities

### 2.1 Hierarchy Tracking (`active_object`)
- **`hierarchy_push(sc_object_host*)`**: Pushes an object (like a module) onto the stack. Any subsequently created objects become its children.
- **`hierarchy_pop()`**: Pops the object when its scope ends (e.g., end of module constructor).
- **`active_object()`**: Returns the top of the stack.

### 2.2 Name Management (`create_name`)
- Generates fully qualified hierarchical names (e.g., `top.mod.sig`).
- **Collision Resolution**: If a name exists, it appends a unique suffix (via `sc_name_gen`) and issues a warning.
- **Instance Table**: Maps `std::string` (name) -> `sc_object*`.

### 2.3 Module Name Stack
This specific stack is used to solve the C++ constructor limitation (passing names cleanly).
- `push_module_name()` / `pop_module_name()`
- Used by `sc_module` and `sc_module_name` to handshake the name.

## 3. Key Takeaways
1.  **Single Source of Truth**: Used for lookups like `sc_find_object("top.mod.sig")`.
2.  **Lifecycle Management**: Can remove/detach objects, though this is rare.
3.  **External Names**: Can track "external" names that aren't SystemC objects (less common).
