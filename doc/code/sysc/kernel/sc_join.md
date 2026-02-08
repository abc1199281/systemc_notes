# SystemC Join Analysis

> **File**: `ref/systemc/src/sysc/kernel/sc_join.cpp`

## 1. Overview
`sc_join` implements the **Fork-Join** pattern in SystemC. It allows a process to spawn dynamic threads (using `sc_spawn`) and then wait for them to finish execution.

## 2. Mechanism
It uses the **Observer Pattern** (via `sc_process_monitor`) to track thread lifecycles.

### 2.1 Registration (`add_process`)
When you add a process to a join object:
1.  It increments an internal counter `m_threads_n`.
2.  It registers itself as a **monitor** on that process (`handle->add_monitor(this)`).

### 2.2 Monitoring (`signal`)
The `sc_join` class implements the `signal()` callback from `sc_process_monitor`:
```cpp
void sc_join::signal(sc_thread_handle thread_p, int type) {
    if (type == sc_process_monitor::spm_exit) {
        // Thread has died
        if ( --m_threads_n == 0 ) {
            // All watched threads are dead
            m_join_event.notify();
        }
    }
}
```

### 2.3 Waiting
The user typically calls `wait(my_join.event())`. The `m_join_event` only fires when the last monitored thread exits.

---

## 3. Usage Pattern (`SC_FORK` / `SC_JOIN`)
While `sc_join` can be used manually, it's the backend for the `SC_FORK ... SC_JOIN` macros (or `sc_spawn` + vector of handles).

```cpp
// Conceptual usage
sc_join my_join;
sc_process_handle h1 = sc_spawn(...);
sc_process_handle h2 = sc_spawn(...);
my_join.add_process(h1);
my_join.add_process(h2);
wait(my_join.event()); // Blocks until BOTH h1 and h2 terminate
```

---

## 4. Hardware / RTL Mapping
This pattern is less common in synthesizeable RTL (which is usually fixed hierarchy), but maps to:
- **Testbench Fork/Join**: SystemVerilog `fork ... join` or `fork ... join_any`.
- **HLS**: Parallel sections of code that must re-synchronize before proceeding.

---

## 5. Key Takeaways
1.  **Dynamic vs Static**: `sc_join` handles dynamic processes (born at runtime).
2.  **Zero Overhead Monitoring**: It uses the built-in monitor pointer in `sc_thread_process`, so it doesn't poll; it's event-driven.
