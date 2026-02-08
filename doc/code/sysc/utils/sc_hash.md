# SystemC Utils: Hash Table (`sc_hash`)

> **Source**: `ref/systemc/src/sysc/utils/sc_hash.cpp`

## 1. Overview
`sc_phash_base` implements a pointer-based **hash table**. It is used internally for mapping keys (often pointers or strings) to values (often pointers).

## 2. Implementation
-   **Chaining**: Uses linked lists (`bins` array of `sc_phash_elem*`) to resolve hash collisions.
-   **Move-To-Front (MTF)**: If `reorder_flag` is true, accessed elements are moved to the front of their collision chain. this acts as a heuristic to speed up repeated accesses to the same item.
-   **Growth**: Automatically `rehash()` (expands table and re-maps entries) when the load factor (`num_entries / num_bins`) exceeds `max_density`.

## 3. Key Functions
-   **`find_entry`**: Core lookup logic.
-   **`do_hash`**: calls the user-provided hash function.
-   **`insert` / `remove`**: Standard API.
-   **`sc_phash_base_iter`**: Iterator support.

## 4. Default Hash Algorithms
Provides default hash functions for common types:
-   `default_ptr_hash_fn`: Hashes a `void*`.
-   `default_str_hash_fn`: Hashes a C-string (`char*`).
-   `default_int_hash_fn`: Hashes an integer.

## 5. Usage
Primarily used for internal lookups, such as mapping process handles or string names to objects.
