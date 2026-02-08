# SystemC Simple Bus Example Analysis

> **Source**: `ref/systemc/examples/sysc/simple_bus`

## 1. Overview
The **Simple Bus** is a classic SystemC example demonstrating how to model a communication bus using **Hierarchical Channels** and custom interfaces. Unlike TLM 2.0, which uses generic payloads and sockets, this example uses explicit port-based communication and custom interface classes.

## 2. Architecture

### 2.1 The Bus Channel (`simple_bus`)
The bus is implemented as a custom channel extending `sc_module` and implementing three interfaces:
1.  **Blocking (`simple_bus_blocking_if`)**: Supports `burst_read` and `burst_write`. Masters using this are suspended (`wait`) until the transaction completes.
2.  **Non-Blocking (`simple_bus_non_blocking_if`)**: Supports `read`, `write`, and `get_status`. Masters initiate a request and check its status later.
3.  **Direct (`simple_bus_direct_if`)**: Supports debug access (`direct_read`, `direct_write`) without consuming simulation time.

### 2.2 Arbitration (`simple_bus_arbiter`)
The arbiter selects which request is granted access to the bus.
-   **Priority Scheme**:
    1.  **Locked Burst**: An active locked burst cannot be interrupted.
    2.  **Lock Granted**: A master that was granted a lock in the previous cycle retains it.
    3.  **Static Priority**: Lower `priority` ID wins.

### 2.3 Slaves (`simple_bus_slave_if`)
Slaves must implement the `simple_bus_slave_if`.
-   **Atomic Operations**: The bus calls `read()` or `write()` on the slave.
-   **Status**: Slaves return `SIMPLE_BUS_OK`, `SIMPLE_BUS_WAIT` (bus retries next cycle), or `SIMPLE_BUS_ERROR`.

## 3. Execution Flow (Blocking Burst)
1.  **Master**: Calls `bus_port->burst_read(priority, ...)`.
2.  **Bus (Interface)**: Creates a `simple_bus_request`, pushes it to `m_requests`, and calls `wait(request->transfer_done)`.
3.  **Bus (Main Action)**:
    -   Runs on falling clock edge.
    -   Calls `get_next_request()`, which queries the **Arbiter**.
    -   If granted, calls `handle_request()`.
4.  **Bus (Handle Request)**:
    -   Maps address to slave.
    -   Calls `slave->read/write()`.
    -   If `OK`, advances burst counter. If burst finished, notifies `transfer_done`.
5.  **Master**: Wakes up from `wait()`.

## 4. Comparison with TLM 2.0
| Feature | Simple Bus (Classic) | TLM 2.0 |
| :--- | :--- | :--- |
| **Communication** | Custom Interfaces & Channels | Generic Payload & Sockets |
| **Timing** | Cycle-Accurate (Clocked) | LT (Zero/Annotated) or AT (Phased) |
| **Interoperability** | Low (Custom Interfaces) | High (Standard Protocol) |
| **Simulation Speed** | Slower (Wait per cycle) | Faster (Direct Function Call) |
