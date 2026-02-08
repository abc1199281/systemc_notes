# TLM Analysis Port Analysis

> **File**: `ref/systemc/src/tlm_core/tlm_1/tlm_analysis/tlm_analysis_port.h`

## 1. Overview
The `tlm_analysis_port` provides a broadcast mechanism for transactions. Unlike standard initiator/target ports which are usually 1-to-1, an analysis port is **1-to-many**.

## 2. Implementation
- **Container**: Uses `std::deque<tlm_analysis_if<T>*>` to store pointers to all bound interfaces (subscribers).
- **Bind**: `bind()` adds an interface to the list.
- **Unbind**: `unbind()` removes an interface.
- **Write**: The `write(const T& t)` method iterates through the list and calls `write(t)` on every bound subscriber.

## 3. Usage
It is primarily used for **passive components** that need to observe traffic without interfering with the transaction flow:
- **Scoreboards**: To check correctness.
- **Coverage Collectors**: To track which addresses/modes have been exercised.
- **Loggers/Monitors**: To print traces.

## 4. Key Takeaways
1.  **Observer Pattern**: It implements the observer pattern.
2.  **Zero Time**: Analysis ports are typically used for zero-time, non-blocking communication (calls finish immediately).
