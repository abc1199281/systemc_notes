# SystemC Data Types: Masks (`sc_int64_mask`)

> **Source**: `ref/systemc/src/sysc/datatypes/int/sc_int64_mask.cpp`

## 1. Overview
This file defines a massive **static lookup table** `mask_int` used for fast bit manipulation and part selection.

## 2. The Table: `mask_int`
-   **Type**: `const uint_type mask_int[SC_INTWIDTH][SC_INTWIDTH]` (64x64).
-   **Purpose**: To get a mask where bits from index `right` to `left` are set (or cleared).
-   **Content**: `mask_int[left][right]` typically contains a mask where bits between `left` and `right` (inclusive) are set to 0 (based on usage in other files like `~mask_int[l][r]`).

## 3. Usage
-   Used heavily in `sc_int_base`, `sc_uint_base`, and their subref classes.
-   Avoids computing masks at runtime using shifts (`(1 << (l-r+1)) - 1 << r`), sacrificing binary size for speed.

## 4. Key Takeaways
-   Optimization for 64-bit integer part selections.
-   This file is almost entirely data.
