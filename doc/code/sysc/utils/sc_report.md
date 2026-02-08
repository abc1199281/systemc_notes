# SystemC Utils: Report (`sc_report`)

> **Source**: `ref/systemc/src/sysc/utils/sc_report.cpp`

## 1. Overview
The `sc_report` class acts as a container for all information regarding a specific report/error instance in SystemC. It is exception-safe and is thrown when `SC_THROW` action is executed.

## 2. Key Member Variables
-   **`severity`**: Level of the report (`SC_INFO`, `SC_WARNING`, `SC_ERROR`, `SC_FATAL`).
-   **`md`**: Pointer to `sc_msg_def` (message definition).
-   **`msg`**: The actual error message string.
-   **`file`** / **`line`**: Source location.
-   **`timestamp`**: Simulation time when report occurred.
-   **`process_name`**: Name of the process triggering the report.

## 3. Functionality
-   **Construction**: Captures current simulation context (time, process name).
-   **Swapping**: Supports efficient `swap` to avoid deep copies where possible.
-   **Deprecated Features**: methods like `register_id` warn about deprecated integer-based IDs (SystemC now uses string-based message types).

## 4. Usage
Users typically interact with this class when catching exceptions:
```cpp
try {
    // ... code that might throw
} catch (const sc_core::sc_report& err) {
    std::cout << "Caught: " << err.what() << std::endl;
}
```
