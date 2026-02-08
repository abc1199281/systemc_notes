# SystemC Utils: Memory Pool (`sc_mempool`)

> **Source**: `ref/systemc/src/sysc/utils/sc_mempool.cpp`

## 1. Overview
`sc_mempool` is a custom memory allocator designed to efficiently manage small objects in SystemC. It aims to reduce the overhead of `malloc`/`free` for frequently allocated/deallocated small items (like events or signals in some contexts).

## 2. Architecture
-   **`sc_allocator`**: The worker class. Each allocator instance handles a specific "cell size" (e.g., 8 bytes, 16 bytes).
    -   It maintains a **free list** of recycled cells.
    -   It allocates large **blocks** (chunks) of memory from the system and carves them into cells when the free list is empty.
-   **`sc_mempool`**: The public interface (static).
    -   Routes an allocation request of size `sz` to the appropriate `sc_allocator`.
    -   If the size is too large for the pool, it falls back to the system's `new`.

## 3. Configuration
-   **`SYSTEMC_MEMPOOL_DONT_USE`**: Environment variable. If set to 1, the memory pool is bypassed, and standard `new`/`delete` are used (useful for debugging with Valgrind/Purify).

## 4. Leak Note
The memory blocks allocated by `sc_mempool` are *never freed* back to the system until the program terminates. This is by design for performance but might be flagged as "in-use" memory by leak detectors.
