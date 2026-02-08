# SystemC Buffer Analysis

> **File**: `ref/systemc/src/sysc/communication/sc_buffer.h`

## 1. Overview
`sc_buffer<T>` is a primitive channel derived from `sc_signal<T>`.

## 2. Difference from `sc_signal`
- **Signal**: Triggers an event *only* if the new value is different from the current value.
- **Buffer**: Triggers an event on *every* write, regardless of whether the value changed.

## 3. Implementation
- **`write()`**: Always sets the new value and calls `request_update()`.
- **`update()`**: Always calls `do_update()`, which triggers the value change event.

## 4. Key Takeaways
1.  **Use Case**: Useful for impulse signaling or when the act of writing itself is significant (e.g., refreshing a display, sending a "heartbeat"), even if the data payload is unchanged.
