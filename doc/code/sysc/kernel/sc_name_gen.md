# SystemC Name Generator Analysis

> **File**: `ref/systemc/src/sysc/kernel/sc_name_gen.cpp`

## 1. Overview
A helper class used to ensure unique names within a specific scope.

## 2. Logic
It maintains a map: `basename` -> `counter`.
- **First instance**: `basename` (if `preserve_first` is true) or `basename_0`.
- **Subsequent**: `basename_1`, `basename_2`, etc.

## 3. Usage
- Used by `sc_object_host::gen_unique_name()` to ensure child names don't collide.
- Used by `sc_object_manager` when resolving global name collisions.

## 4. Key Takeaways
1.  **Determinism**: Ensures that automatic naming is deterministic based on construction order.
