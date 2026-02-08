# SystemC Utils: Report Handler (`sc_report_handler`)

> **Source**: `ref/systemc/src/sysc/utils/sc_report_handler.cpp`

## 1. Overview
`sc_report_handler` is the central static class that dispatches all SystemC reports. It determines *what to do* (actions) when a report with a specific message type and severity occurs.

## 2. Configuration Mechanisms
-   **Actions**: What to do for a report.
    -   `SC_DO_NOTHING`, `SC_DISPLAY`, `SC_LOG`, `SC_THROW`, `SC_STOP`, `SC_ABORT`.
-   **Limits**: `stop_after(severity, count)` allows stopping simulation after N errors/warnings.
-   **Overrides**:
    -   `set_actions(severity, action)`: Default action for a severity.
    -   `set_actions(msg_type, action)`: Override action for specific message type.

## 3. Internal logic (`execute` method)
The `execute` method determines final actions by priority:
1.  **Forced** actions (highest priority).
2.  **Suppressed** actions.
3.  **Message Type** specific actions.
4.  **Severity** default actions.

## 4. Output Handling
-   **Default Handler**: Prints to `std::cout` (if `SC_DISPLAY`) and logs to file (if `SC_LOG`).
-   **Log File**: Managed via `sc_log_file_handle`, supports `set_log_file_name`.
-   **Caching**: Can cache the last report (`SC_CACHE_REPORT`) for retrieval.

## 5. Global State
It maintains global counters (`sev_call_count`) for the number of infos, warnings, errors, and fatals occurred.
