# SystemC Utils: Priority Queue (`sc_pq`)

> **Source**: `ref/systemc/src/sysc/utils/sc_pq.cpp`

## 1. Overview
`sc_ppq_base` describes a generic priority queue using a **binary heap** implementation. It is used within the SystemC kernel to manage timed events (the "pending" set of events that need to trigger at specific simulation times).

## 2. Implementation
-   **Structure**: Array-based binary heap (`m_heap`).
-   **Indexing**: 1-based indexing for convenient child/parent calculation:
    -   Left child: `2*i`
    -   Right child: `2*i + 1`
    -   Parent: `i/2`
-   **Resizing**: Automatically grows (`m_size_alloc`) when the heap becomes full.

## 3. Key Operations
-   **`insert(elem)`**: Adds a new element and "bubbles" it up to maintain heap property.
-   **`extract_top()`**: Removes the root (smallest/largest depending on comparison) and "heapifies" down to restore order.
-   **`compar`**: A function pointer passed at construction to determine element ordering.

## 4. Usage in Kernel
Crucial for the discrete-event simulation kernel to efficiently fetch the *next* event to process in time order.
