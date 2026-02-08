# SystemC Reset Analysis

> **File**: `ref/systemc/src/sysc/kernel/sc_reset.cpp`

## 1. Overview
This file implements the mechanism for **Asynchronous** and **Synchronous** resets for processes. It allows processes (`CTHREAD`, `THREAD`, `METHOD`) to register sensitivity to a reset signal.

## 2. Core Mechanism

### 2.1 Lazy Binding with `sc_reset_finder`
When a user calls `reset_signal_is(port, ...)` in a constructor, the port might not be bound to a signal yet.
- **Problem**: We need the underlying `sc_signal` to register the process, but we only have a port.
- **Solution**: `sc_reset_finder` captures the port and the process. Later, during `reconcile_resets()` (likely at end of elaboration), it resolves the port to the actual interface/signal and performs the registration.

### 2.2 `sc_reset` Class
This class is attached to `sc_signal<bool>` instances that act as resets.
- **`m_targets`**: A list of processes sensitive to this reset.
- **`notify_processes()`**: Called when the signal changes. It iterates over `m_targets` and calls `reset_changed()` on the affected processes.

## 3. Hardware / RTL Mapping
| SystemC Concept | Hardware Concept |
| :--- | :--- |
| `reset_signal_is(..., true/false)` | Async/Sync Reset pin (Active High/Low) |
| `active` state in `sc_reset` | Reset assertion |

## 4. Key Takeaways
1.  **Multiple Resets**: A process can rely on multiple reset signals.
2.  **Thread vs Method**:
    - **Methods**: Reset check happens before execution.
    - **Threads**: Must call `wait()` to potentially sense reset (or throw exception if async).
