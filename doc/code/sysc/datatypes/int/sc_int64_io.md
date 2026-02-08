# SystemC Data Types: IO (`sc_int64_io`)

> **Source**: `ref/systemc/src/sysc/datatypes/int/sc_int64_io.cpp`

## 1. Overview
This file provides the `operator<<` implementations for printing `int64` and `uint64` to `std::ostream`.

## 2. Why is this needed?
Standard C++ `iostream` support for 64-bit integers was inconsistent across older compilers (especially MSVC vs GCC) when SystemC was designed. This module ensures consistent formatting:
-   **Hex/Octal**: Handles `std::ios::hex` and `std::ios::oct` flags.
-   **Showbase**: Handles `0x` or `0` prefixes.
-   **Padding**: Respects `std::setw` and `std::setfill`.

## 3. Implementation
It manually formats the number into a buffer:
```cpp
static void write_uint64(::std::ostream& os, uint64 val, int sign) { ... }
```
It iterates through digits (mod 10, 8, or 16) to build the string.

## 4. Key Takeaways
-   Ensures portable output formatting for SystemC's core 64-bit types.
