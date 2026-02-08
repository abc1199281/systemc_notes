# SystemC Data Types: sc_bv_base

> **Source**: `ref/systemc/src/sysc/datatypes/bit/sc_bv_base.cpp`

## 1. Overview
`sc_bv_base` is the base class for **arbitrary-sized bit vectors** (`sc_bv<N>`). It represents a vector of 2-valued bits (`0` and `1`).

## 2. Implementation

### 2.1 Storage (`m_data`)
-   Uses an array of `sc_digit` (usually `unsigned long` or `unsigned int`) to store bits.
-   **Size Calculation**: `m_size = (m_len - 1) / SC_DIGIT_SIZE + 1`.
-   **Optimization**: If the vector is small (fits in `SC_BASE_VEC_DIGITS`), it uses a pre-allocated member array `m_base_vec` to avoid heap allocation (`new`).

### 2.2 String Parsing (`convert_to_bin`)
Handles various string formats:
-   **Binary**: "0b1011"
-   **Hex**: "0xWA"
-   **Formatted vs Unformatted**:
    -   Strings starting with "0b", "0x", etc. are treated as formatted.
    -   Raw strings "1011" are treated as unformatted.
    -   It cleans up prefixes and redundant bits.

## 3. Key Takeaways
-   **2-Valued Only**: Cannot store `X` or `Z`. Attempting to assign `X` or `Z` usually results in a conversion to `0` or an error, depending on the context.
-   **Efficiency**: More efficient than `sc_lv` because it doesn't need a control word for X/Z states.
