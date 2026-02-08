# TLM-2.0 Core: Generic Payload Implementation (`tlm_gp`)

> **Source**: `ref/systemc/src/tlm_core/tlm_2/tlm_generic_payload/tlm_gp.cpp`

## 1. Overview
This file implements the `tlm_generic_payload` class and the underlying extension mechanism. It handles the dynamic registration of extensions and the deep copy logic for transactions.

## 2. Extension Mechanism (`tlm_extension_registry`)
-   **Singleton Registry**: `tlm_extension_registry` maps `std::type_index` (from `typeid`) to a unique integer ID (`unsigned int`).
-   **Registration**: `register_extension` assigns a new ID if the type hasn't been seen before.
-   **Usage**: `tlm_extension_base` uses this registry to get IDs for derived extension classes.

## 3. Generic Payload Implementation
-   **Construction**: Initializes all fields (address, command, data ptr, etc.) to defaults.
-   **Deep Copy** (`deep_copy_from`):
    -   Copies standard attributes (address, command, etc.).
    -   **Data**: `memcpy` data buffer if present.
    -   **Byte Enables**: `memcpy` byte enable buffer if present.
    -   **Extensions**: Iterates through extensions.
        -   If original has extension `i` and we don't: calls `clone()`.
        -   If both have extension `i`: calls `copy_from()`.
-   **Update Original** (`update_original_from`):
    -   Used in `transport_dbg` or return path of transport.
    -   Copies back extensions, response status, DMI hint.
    -   **Read Data**: If command is READ, copies data back from the copy to the original, respecting byte enables (optimized for 4/8 byte words).
-   **Memory Management**:
    -   `free_all_extensions()`: Explicitly frees all extensions.
    -   Destructor: Frees extensions that are still attached.

## 4. Key Helpers
-   `set_extension`, `get_extension`, `clear_extension`: Accessors using the extension array.
-   `resize_extensions`: Expands the extension array to `max_num_extensions()` if new extensions were registered globally.
