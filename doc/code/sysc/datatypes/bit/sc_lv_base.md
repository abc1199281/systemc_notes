# SystemC Data Types: sc_lv_base

> **Source**: `ref/systemc/src/sysc/datatypes/bit/sc_lv_base.cpp`

## 1. Overview
`sc_lv_base` is the base class for **arbitrary-sized logic vectors** (`sc_lv<N>`). Unlike `sc_bv`, it supports 4-valued logic (`0`, `1`, `X`, `Z`).

## 2. Implementation: The 4-Valued Encoding

### 2.1 Dual-Word Storage
To represent 4 values per bit, `sc_lv_base` uses **two parallel arrays** of `sc_digit`:
1.  **`m_data` (Data Word)**
2.  **`m_ctrl` (Control Word)**

| Logic Value | Control Bit (`cw`) | Data Bit (`dw`) |
| :--- | :---: | :---: |
| **0** | 0 | 0 |
| **1** | 0 | 1 |
| **Z** | 1 | 0 |
| **X** | 1 | 1 |

-   **0/1**: Control bit is 0. Data bit holds the value.
-   **Z/X**: Control bit is 1. Data bit distinguishes between Z and X.

### 2.2 Memory Layout
-   It allocates a single block of memory of size `2 * m_size`.
-   `m_data` points to the start.
-   `m_ctrl` points to the middle (`m_data + m_size`).

## 3. Operations
-   **`is_01()`**: Checks if the vector contains any X or Z. Implementation simply checks if all words in `m_ctrl` are 0.
-   **Assignment**: When assigning from string, it parses characters and sets individual bits using `sc_logic::to_value(c)`.

## 4. Key Takeaways
-   **Hardware Modeling**: This is the standard type for modeling buses and signals where high-impedance and unknown states matter.
-   **Overhead**: Requires 2x memory compared to `sc_bv` (plus encoding overhead) and is slower due to bitwise operations on two words.
