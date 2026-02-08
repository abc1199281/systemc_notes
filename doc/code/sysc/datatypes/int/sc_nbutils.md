# SystemC Data Types: Number Utilities (`sc_nbutils`)

> **Source**: `ref/systemc/src/sysc/datatypes/int/sc_nbutils.cpp`

## 1. Overview
Provides external utility functions for `sc_signed` and `sc_unsigned` classes, particularly for string parsing.

## 2. Global Storage
-   `sc_temporary_digits`: A global heap (`sc_digit_heap`) used to allocate temporary digits for intermediate calculations in arbitrary-precision arithmetic. This reduces `malloc`/`free` overhead.

## 3. Parsing Functions
-   **`parse_binary_bits`**: Parses a binary string (0, 1, X, Z) into `sc_digit` arrays (data and control).
    -   Handles logic values (X/Z) by setting bits in the `ctrl_p` array.
-   **`parse_hex_bits`**: Parses a hex string into `sc_digit` arrays.
    -   Handles X/Z in hex (which represent 4 bits of X/Z).

## 4. Key Takeaways
-   Critical for `assign_from_string` in big integer types.
-   Handles the complexity of mapping string characters to bit/control words.
