# SystemC Semaphore Analysis

> **File**: `ref/systemc/src/sysc/communication/sc_semaphore.cpp`

## 1. Overview
`sc_semaphore` is a primitive channel providing counting semaphore functionality. It implements `sc_semaphore_if`.

## 2. Implementation

### 2.1 State Variables
- `m_value`: The current semaphore count (integer).
- `m_free`: An `sc_event` notified when a resource is returned (`post`).

### 2.2 Mechanism
- **`wait()`**:
    - Checks if `m_value > 0`.
    - If `0`, calls `wait(m_free)` to block.
    - Decrements `m_value` after acquiring.
- **`post()`**:
    - Increments `m_value`.
    - Calls `m_free.notify()` to wake up waiting threads.

## 3. Key Takeaways
1.  **Blocking**: Like mutex, `wait()` allows limited resource access scheduling (e.g., limiting access to a bus to N masters).
