# TLM Utils: Convenience Socket Bases (`convenience_socket_bases`)

> **Source**: `ref/systemc/src/tlm_utils/convenience_socket_bases.cpp`

## 1. Overview
This file implements base classes for the "convenience sockets" (simple sockets, multi-sockets, passthrough sockets) in the `tlm_utils` namespace. It mostly handles common error reporting and elaboration checks.

## 2. Classes
-   **`convenience_socket_base`**:
    -   Provides `display_warning` and `display_error`.
    -   Formats messages with the socket name: `socket_name: message`.
-   **`simple_socket_base`**:
    -   `get_report_type()`: Returns `"/OSCI_TLM-2/simple_socket"`.
    -   `elaboration_check()`: Displays an error if certain actions (like callback registration) are attempted after elaboration is done.
-   **`passthrough_socket_base`**:
    -   Returns report type `"/OSCI_TLM-2/passthrough_socket"`.
-   **`multi_socket_base`**:
    -   Returns report type `"/OSCI_TLM-2/multi_socket"`.

## 3. Purpose
These classes reduce code duplication in the header-only implementations of the convenience sockets by moving non-template logic (like string formatting and checks) into a compiled source file.
