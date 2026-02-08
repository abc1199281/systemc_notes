# SystemC Interface Analysis

> **File**: `ref/systemc/src/sysc/communication/sc_interface.cpp`

## 1. Overview
`sc_interface` is the abstract base class for all interface classes in SystemC. Interfaces define the set of operations (methods) that a channel must implement and a port can call.

## 2. Core Methods

### 2.1 `register_port`
- **Signature**: `virtual void register_port(sc_port_base& port, const char* if_typename)`
- **Purpose**: Called when a port is bound to this interface.
- **Default**: Does nothing. Channels can override this to track which ports are connected to them (e.g., for arbitration or connectivity checks).

### 2.2 `default_event`
- **Signature**: `virtual const sc_event& default_event() const`
- **Purpose**: Returns the default event associated with this interface.
- **Use Case**: Used by `sensitive << port;`. If a process is sensitive to a port, it actually becomes sensitive to the interface's `default_event()`.
- **Default**: Warns `SC_ID_NO_DEFAULT_EVENT_` and returns `sc_event::none`.

## 3. Key Takeaways
1.  **Pure Virtual Destructor**: It ensures proper cleanup of derived channel objects.
2.  **Contract**: It strictly defines the "API" part of the communication channel.
