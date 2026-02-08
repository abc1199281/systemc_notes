# TLM-2.0 Core: Phase Implementation (`tlm_phase`)

> **Source**: `ref/systemc/src/tlm_core/tlm_2/tlm_generic_payload/tlm_phase.cpp`

## 1. Overview
This file implements `tlm_phase`, the class representing the phase of a transaction (e.g., `BEGIN_REQ`, `END_RESP`). It uses a registry pattern similar to extensions to support user-defined phases.

## 2. Architecture (`tlm_phase_registry`)
-   **Singleton**: `tlm_phase_registry` manages the mapping between phase types and IDs.
-   **Storage**:
    -   `ids_`: Map of `std::type_index` -> `unsigned int` (ID).
    -   `names_`: Vector of strings, indexed by ID.
-   **Built-in Phases**: The constructor pre-registers standard phases:
    -   `UNINITIALIZED_PHASE`
    -   `BEGIN_REQ`, `END_REQ`
    -   `BEGIN_RESP`, `END_RESP`

## 3. Functionality
-   **Registration**: `register_phase` ensures a unique ID for each `std::type_info`. It is thread-safe effectively by being initialized once.
-   **Name Retrieval**: `get_name()` returns the string representation of the phase from the registry.

## 4. Usage
Users typically don't interact with the registry directly. They instantiate `tlm_phase` (or derived classes/constants), which calls `register_phase` internally on construction.
