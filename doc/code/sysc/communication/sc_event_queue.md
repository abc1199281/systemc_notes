# SystemC Event Queue Analysis

> **File**: `ref/systemc/src/sysc/communication/sc_event_queue.cpp`

## 1. Overview
`sc_event_queue` allows multiple notifications to be pending for the same channel, unlike `sc_event` which ignores subsequent notifications if one is already pending for the same time.

## 2. Mechanism

### 2.1 Priority Queue (`m_ppq`)
- Stores all requested notification times.
- Ordered by time (earliest first).

### 2.2 `notify(time)`
- Pushes the time onto the queue.
- If this is the earliest event, it notifies the underlying `sc_event m_e`.

### 2.3 `fire_event`
- An `SC_METHOD` sensitive to `m_e`.
- Pops the top event from the queue.
- If there are more events in the queue, it re-notifies `m_e` for the delta between the current time and the next event time.

## 3. Key Takeaways
1.  **Use Case**: Essential for modeling things like packet arrival queues where multiple packets might arrive at the exact same simulation time or very close together, and you don't want to lose any notifications.
