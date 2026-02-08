# SystemC Utils: List (`sc_list`)

> **Source**: `ref/systemc/src/sysc/utils/sc_list.cpp`

## 1. Overview
`sc_plist_base` implements a generic **doubly linked list**. It provides standard list operations which are used throughout the SystemC kernel for managing collections of objects that require efficient insertion/removal from arbitrary positions.

## 2. Components
-   **`sc_plist_elem`**: The node class. Contains:
    -   `data`: `void*` pointer to the content.
    -   `prev`, `next`: Pointers to neighbors.
    -   Uses `sc_mempool` for allocation to minimize overhead.
-   **`sc_plist_base`**: The list container.
    -   Head/Tail pointers.
    -   Methods: `push_back`, `push_front`, `pop_back`, `pop_front`, `insert_before`, `insert_after`.
-   **`sc_plist_base_iter`**: Iterator for traversing the list.

## 3. Characteristics
-   **Void Pointers**: It stores `void*`, making it type-unsafe directly. Type-safe wrappers (templates) usually layer on top of this base to provide `sc_list<T>`.
-   **Custom Memory**: Heavily relies on `sc_mempool` for node creation/destruction.
