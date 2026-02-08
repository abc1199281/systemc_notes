# SystemC Data Types: Length Parameter (`sc_length_param`)

> **Source**: `ref/systemc/src/sysc/datatypes/int/sc_length_param.cpp`

## 1. Overview
A helper class to wrap a simple integer length.

## 2. Usage
-   Used in conjunction with `sc_context` to pass length information implicitly in some SystemC constructs (though less common in modern user code).
-   Primarily wraps an `int m_len`.

## 3. Key Methods
-   `to_string()` / `print()` / `dump()`: Standard debugging output.
