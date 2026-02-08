# SystemC Mutex Analysis

> **File**: `ref/systemc/src/sysc/communication/sc_mutex.cpp`

## 1. Overview
`sc_mutex` (Mutual Exclusion) is a primitive channel that provides mutually exclusive access to a resource. It implements `sc_mutex_if`.

## 2. Implementation

### 2.1 State Variables
- `m_owner`: Stores the `sc_process_b*` of the thread that currently holds the lock with `0` indicating it is free.
- `m_free`: An `sc_event` used to wake up waiting threads when the mutex is unlocked.

### 2.2 Mechanism
- **`lock()`**:
    - If `m_owner` matches the current process, it returns 0 (re-entrant/safe).
    - If `in_use()`, it calls `wait(m_free)` to block the current thread.
    - Once free, it sets `m_owner` to the current process.
- **`unlock()`**:
    - Verifies valid owner.
    - Sets `m_owner = 0`.
    - Calls `m_free.notify()` to wake up *all* waiting threads. (Note: Only one will start running in the next delta cycle/step due to SystemC's co-operative non-preemptive scheduling, but they will race for the lock).

## 3. Key Takeaways
1.  **Thread Affinity**: Mutexes are owned by threads (`sc_thread_process`). Method processes cannot call `lock()` because it might wait.
2.  **RAII**: The header `sc_mutex_if.h` defines `sc_scoped_lock` for safe exception handling.
