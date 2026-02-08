# SystemC Wait Analysis

> **Files**:
> - `ref/systemc/src/sysc/kernel/sc_wait.cpp`
> - `ref/systemc/src/sysc/kernel/sc_wait_cthread.cpp`

## 1. Overview
`wait()` is the primary mechanism for **Process Synchronization** in SystemC. It temporarily pauses the execution of a thread and yields control back to the simulation kernel (`sc_simcontext::crunch`).

## 2. The `wait()` Facade
The global `wait()` functions in `sc_wait.cpp` act as a **Facade** / **Dispatcher**. They do not contain the logic for suspending execution themselves; instead, they delegat to the current process handle.

### 2.1 Dispatch Logic
```cpp
void wait( const sc_event& e, sc_simcontext* simc ) {
    sc_curr_proc_handle cpi = simc->get_curr_proc_info();
    switch( cpi->kind ) {
    case SC_THREAD_PROC_:
        // Delegate to thread implementation
        reinterpret_cast<sc_thread_handle>( cpi->process_handle )->wait( e );
        break;
    case SC_CTHREAD_PROC_:
         // Delegate to cthread implementation (plus cycle handling)
         // ...
        break;
    default:
        // ERROR: methods cannot wait!
        SC_REPORT_ERROR( SC_ID_WAIT_NOT_ALLOWED_, ... );
    }
}
```

### 2.2 `next_trigger()`
For `SC_METHOD`, the equivalent function is `next_trigger()`. Since methods cannot suspend (no stack), `next_trigger()` simply tells the kernel: "When this method finishes, make it sensitive to *this* event for the next run."

---

## 3. `SC_CTHREAD` Specifics
`sc_wait_cthread.cpp` adds specialized wait functions for Clocked Threads:
- **`wait(N)`**: Wait for N clock cycles. This calls `wait_cycles(n)`.
- **`halt()`**: Stops the thread indefinitely (essentially `wait()` without arguments, but intended to mean "done").
- **`at_posedge(sig)`**: Loops `do { wait(); } while (...)` until the signal matches the edge. This is effectively a "poll loop" synchronized to the clock.

---

## 4. Hardware / RTL Mapping

| SystemC | Verilog / SystemVerilog |
| :--- | :--- |
| `wait(10, SC_NS)` | `#10` |
| `wait(event)` | `@(event)` |
| `wait(N)` (CTHREAD) | `repeat(N) @(posedge clk)` |
| `next_trigger(e)` | Changing the sensitivity list (dynamic) |

### 4.1 Synthesis Implications
- **`wait()`** in `SC_THREAD`: Usually implies a clock boundary in High-Level Synthesis (HLS), effectively inserting registers.
- **`wait(N)`**: Inserts a state machine chain of N states (waiting N clocks).

---

## 5. Key Takeaways
1.  **Context Aware**: `wait()` knows which process is calling it. It throws a runtime error if you call it from `SC_METHOD`.
2.  **Yielding**: Every `wait()` yields execution. If a thread loops `while(1) {}` without a `wait()`, it hangs the simulation (starves the kernel).
3.  **Static vs Dynamic**:
    - `sensitive << e` (Static): Defined at construction, always active when at `wait()`.
    - `wait(e)` (Dynamic): Temporarily overrides static sensitivity (depending on exact wait type, usually just waits for `e`).
