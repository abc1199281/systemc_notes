# SystemC Data Types: 32-bit Masks (`sc_int32_mask`)

> **Source**: `ref/systemc/src/sysc/datatypes/int/sc_int32_mask.cpp`

## 1. Overview
This file defines the `mask_int` lookup table for 32-bit environments (`_32BIT_` defined).

## 2. The Table
-   **Structure**: 32x32 array of `uint_type`.
-   **Purpose**: Same as `sc_int64_mask`, but used when `SC_INTWIDTH` is 32 (legacy or specific 32-bit build configurations).

## 3. Key Takeaways
-   Ensures optimization for bit selection even on 32-bit systems.
