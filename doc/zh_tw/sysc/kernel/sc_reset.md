# SystemC Reset 分析

> **檔案**: `ref/systemc/src/sysc/kernel/sc_reset.cpp`

## 1. 概述
此檔案實作行程的 **非同步 (Asynchronous)** 和 **同步 (Synchronous)** 重置機制。它允許行程 (`CTHREAD`, `THREAD`, `METHOD`) 註冊對重置訊號的敏感度。

## 2. 核心機制

### 2.1 使用 `sc_reset_finder` 的延遲綁定
當使用者在建構子中呼叫 `reset_signal_is(port, ...)` 時，port 可能尚未綁定到訊號。
- **問題**: 我們需要底層的 `sc_signal` 來註冊行程，但我們只有 port。
- **解決方案**: `sc_reset_finder` 捕捉 port 和 process。稍後，在 `reconcile_resets()` 期間 (可能在 elaboration 結束時)，它將 port 解析為實際的介面/訊號並執行註冊。

### 2.2 `sc_reset` 類別
此類別附加到充當重置的 `sc_signal<bool>` 實例上。
- **`m_targets`**: 對此重置敏感的行程清單。
- **`notify_processes()`**: 當訊號改變時呼叫。它遍歷 `m_targets` 並在受影響的行程上呼叫 `reset_changed()`。

---

## 3. 硬體 / RTL 對應

| SystemC 概念 | 硬體概念 |
| :--- | :--- |
| `reset_signal_is(..., true/false)` | 非同步/同步 重置腳位 (Active High/Low) |
| `sc_reset` 中的 `active` 狀態 | 重置斷言 (Assertion) |

---

## 4. 關鍵重點
1.  **多重重置**: 一個行程可以依賴多個重置訊號。
2.  **Thread vs Method**:
    - **Methods**: 重置檢查發生在執行之前。
    - **Threads**: 必須呼叫 `wait()` 才能潛在感知重置 (或是如果是非同步重置則丟出例外)。
