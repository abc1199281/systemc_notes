# SystemC Module Registry 分析

> **檔案**: `ref/systemc/src/sysc/kernel/sc_module_registry.cpp`

## 1. 概述
`sc_module_registry` 是一個專門的容器，由模擬上下文 (simulation context) 為了追蹤系統中實例化的 **所有** 模組而使用。它主要用於內部用途，以驅動模擬階段，如 elaboration 和模擬回調 (callbacks)。

## 2. 核心機制

### 2.1 註冊 (Registration)
- **`insert(sc_module&)`**: 由 `sc_module::sc_module_init()` 呼叫。它將模組加入到 `m_module_vec`。
- **檢查 (Checks)**: 它強制執行你不能在 elaboration 完成後或模擬執行期間插入模組。

### 2.2 階段回調 (Phase Callbacks)
註冊表充當廣播器，遍歷所有模組以呼叫其生命週期方法：
- `construction_done()`
- `elaboration_done()`
- `start_simulation()`
- `simulation_done()`

---

## 3. 關鍵重點
1.  **全域清單 (Global List)**: 雖然 `sc_object_manager` 維護 *階層* (樹)，但 `sc_module_registry` 維護所有模組的 *扁平清單*。
2.  **快照 (Snapshot)**: 它允許核心有效率地遍歷所有模組，而無需遍歷物件樹。
3.  **除錯 (Debug)**: 在除錯模式 (`DEBUG_SYSTEMC`) 下，它會檢查重複插入。
