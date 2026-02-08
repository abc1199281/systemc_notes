# Top-Down Analysis: The Event System

SystemC is an **Event-Driven Simulator**. Events are the fundamental mechanism for synchronization and communication between processes.

## 1. The Nature of `sc_event`
An `sc_event` is a **stateless, instantaneous** notification.
-   **No Value**: It carries no data.
-   **No Duration**: It exists only at the moment of notification.
-   **No Memory**: If a process waits for an event *after* it has occurred, it misses it (unless using `event_queue` or flag patterns).

## 2. Notification Types
Events can be triggered in three ways, corresponding to the different phases of the simulation loop:

### 2.1 Immediate Notification
-   **Syntax**: `e.notify()`
-   **Effect**: The event triggers **immediately** within the current Evaluation phase.
-   **Use Case**: Software-like synchronization between processes running in the same delta cycle.
-   **Danger**: Can introduce non-determinism if used with shared variables (race conditions on who runs first/last).

### 2.2 Delta Notification
-   **Syntax**: `e.notify(SC_ZERO_TIME)`
-   **Effect**: The event is scheduled to trigger at the **start of the next delta cycle** (Update/Notify phase).
-   **Use Case**: This is the standard mechanism for **Primitive Channels** (like `sc_signal`). When a signal gets a new value, it requests a delta notification to wake up sensitive processes *after* all updates are complete. This ensures determinism.

### 2.3 Timed Notification
-   **Syntax**: `e.notify(10, SC_NS)`
-   **Effect**: The event is scheduled to trigger when simulation time advances by the specified amount.
-   **Use Case**: Modeling transport delays, clock periods, or timeouts.

## 3. Dynamic Sensitivity
Processes can dynamically wait for events using `wait()` (Thread) or `next_trigger()` (Method).

-   **`wait(e)`**: Suspends the thread until `e` is notified.
-   **`wait(e1 | e2)`**: Suspends until **either** `e1` or `e2` is notified (OR list).
-   **`wait(e1 & e2)`**: Suspends until **both** have been notified (AND list). This requires the kernel to track the state of notifications for this specific `wait` call.

## 4. Kernel Interaction
When `notify()` is called:
1.  **Immediate**: The kernel immediately iterates through the event's list of sensitive processes (`m_methods_dynamic`, `m_threads_dynamic`) and pushes them to the **runnable queue**.
2.  **Delta/Timed**: The event is added to the kernel's **event queue**. When the scheduler reaches the appropriate phase, it "fires" the event, repeating step 1.

## 5. Hardware Mapping
| SystemC Concept | Verilog Equivalent |
| :--- | :--- |
| `sc_event` | `event` (named event) |
| `sc_event.notify()` | `-> e;` |
| `sc_event.notify(0)` | Logic triggering in the next NBA region (conceptually) |
| `wait(e)` | `@(e)` |
