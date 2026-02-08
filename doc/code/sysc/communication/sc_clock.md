# SystemC Clock Analysis

> **File**: `ref/systemc/src/sysc/communication/sc_clock.cpp`

## 1. Overview
`sc_clock` is a predefined primitive channel that generates a periodic clock signal. It inherits from `sc_signal<bool>`.

## 2. Implementation

### 2.1 Initialization
The constructor calculates:
- `m_posedge_time`: Duration high.
- `m_negedge_time`: Duration low.
- `m_start_time`: When to start.

### 2.2 Process Spawning
- **`spawn_edge_method`**: Creates two internal `SC_METHOD` processes: one for posedge, one for negedge.
- These processes are sensitive to `m_next_posedge_event` and `m_next_negedge_event` respectively.

### 2.3 Execution Loop
1.  **Action**: When the method triggers, it updates the clock's value using `write()`.
2.  **Rescheduling**: It then notifies the *next* event (`notify_internal(m_period)`) to trigger itself again after one full period.
- This creates a self-sustaining loop of events driving the clock signal.

## 3. Key Takeaways
1.  **Efficiency**: Clocks are implemented as standard SystemC processes driving a signal, ensuring they integrate perfectly with the scheduler (delta cycles, etc.).
