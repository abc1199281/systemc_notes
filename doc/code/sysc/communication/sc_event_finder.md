# SystemC Event Finder Analysis

> **File**: `ref/systemc/src/sysc/communication/sc_event_finder.cpp`

## 1. Overview
`sc_event_finder` is a helper class used internally by ports to support `sensitive << port.pos();`.

## 2. The Problem
When you write `sensitive << port.pos()`, the port might not be bound yet. Therefore, `port.pos()` cannot return the actual event from the channel because the channel isn't known.

## 3. The Solution
- `port.pos()` returns a specific implementation of `sc_event_finder`.
- The `sc_event_finder` temporarily holds a reference to the port.
- During `complete_binding` or when sensitivity is finalized, the kernel calls `find_event()` on the finder.
- The finder asks the port for its bound interface and *then* retrieves the actual event from that interface.

## 4. Key Takeaways
1.  **Deferred Resolution**: It's a classic "lazy evaluation" pattern to handle the separation between construction time (sensitivity declaration) and elaboration time (binding).
