# SystemC Data Types: sc_bit

> **Source**: `ref/systemc/src/sysc/datatypes/bit/sc_bit.cpp`

## 1. Overview
`sc_bit` is a **deprecated** class intended to represent a single bit. In modern SystemC (IEEE 1666), standard C++ `bool` or `sc_logic` should be used instead.

## 2. Implementation
-   **Storage**: Wraps a `bool` (`m_val`).
-   **Deprecation**: The constructor calls `sc_deprecated_sc_bit()`, which issues a warning `SC_ID_IEEE_1666_DEPRECATION_` ("sc_bit is deprecated, use bool instead") once per simulation.

## 3. Key Takeaways
-   **Do not use new code**.
-   Exists mainly for backward compatibility with very old SystemC models (pre-2.0).
