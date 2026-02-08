# SystemC Data Types: Arbitrary Precision Unsigned Integer (`sc_unsigned`)

> **Source**: `ref/systemc/src/sysc/datatypes/int/sc_unsigned.cpp`

## 1. Overview
`sc_unsigned` is the base for `sc_biguint<W>`. It provides arbitrary-width unsigned integer arithmetic.

## 2. Implementation
-   **Storage**: Similar to `sc_signed`, it uses a dynamic array of `sc_digit`.
-   **Sign Extension**: Unlike `sc_signed` which extends the sign bit, `sc_unsigned` always **zero-extends** when converting to larger types or mixing with signed types (usually treating the signed number as a bit pattern or requiring explicit casts).

## 3. Arithmetic and Logic
-   **Arithmetic**: Uses helper functions in `sc_unsigned_ops.h` (implied). Logic is generally simpler than `sc_signed` due to lack of 2's complement handling for negative numbers.
-   **Concatenation**: `concat_get_data` and `concat_set` handle data movement. `sc_unsigned` ensures that bits outside the specified range are zeroed out.

## 4. Key Takeaways
-   **Mixed Arithmetic**: When mixing `sc_unsigned` and `sc_signed`, the result is typically `sc_signed` to preserve value correctness, or `sc_unsigned` if bit-pattern semantics are desired (SystemC has specific rules for this).
-   **Efficiency**: Slower than `sc_uint`, use only when W > 64.
