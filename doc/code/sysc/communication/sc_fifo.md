# SystemC FIFO Analysis

> **File**: `ref/systemc/src/sysc/communication/sc_fifo.h`

## 1. Overview
`sc_fifo<T>` is a primitive channel that implements a First-In-First-Out buffer. It supports both blocking and non-blocking read/write interfaces.

## 2. Implementation

### 2.1 Storage
- **Circular Buffer**: Uses an array `m_buf` of type `T` and size `m_size`.
- **Indices**: Logic uses `m_ri` (read index) and `m_wi` (write index) to manage the circular wrapping.

### 2.2 Blocking Mechanism
- **Events**:
    - `m_data_read_event`: Notified when data is read (meaning space became available).
    - `m_data_written_event`: Notified when data is written (meaning data became available).
- **Update Phase**:
    - `update()` is called at the end of the delta cycle.
    - It checks `m_num_read` and `m_num_written`.
    - If `m_num_read > 0`, it notifies `m_data_read_event`. This wakes up any writers blocked on a full FIFO.
    - If `m_num_written > 0`, it notifies `m_data_written_event`. This wakes up any readers blocked on an empty FIFO.

### 2.3 Key Methods
- **`read(T&)`**: Blocks on `m_data_written_event` if `num_available() == 0`.
- **`write(const T&)`**: Blocks on `m_data_read_event` if `num_free() == 0`.
- **`nb_read` / `nb_write`**: Non-blocking versions that return `false` immediately if the operation cannot be performed.

## 3. Key Takeaways
1.  **Deterministic**: The use of the request-update mechanism guarantees determinism. A value written in delta cycle `N` is available to be read in delta cycle `N+1` (effectively), because the event notification happens in the update phase.
