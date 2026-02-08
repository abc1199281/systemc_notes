# 自頂向下分析：模擬引擎 (Simulation Engine)

SystemC 本質上是一個專門用於硬體模擬的 **協作式多工作業系統 (co-operative multitasking OS)**。其核心是 `sc_simcontext`，它充當內核 (kernel)、排程器 (scheduler) 和事件管理器 (event manager)。本文件分解了模擬引擎如何驅動時間和事件。

## 1. 啟動序列
在任何模擬執行之前，必須先建立 "世界"。這就是 **細化階段 (Elaboration Phase)**。

1.  **進入點 (Entry Point)**: 使用者撰寫 `sc_main`。函式庫提供 C++ `main`，它會呼叫 `sc_elab_and_sim`。
2.  **建構 (Construction)**: 執行全域建構子。模組 (`sc_module`) 和訊號 (`sc_signal`) 向 `sc_simcontext::m_module_registry` 和 `m_object_manager` 註冊自己。
3.  **細化 (Elaboration)**: 呼叫 `sc_start()`。核心對所有模組呼叫 `before_end_of_elaboration` 和 `end_of_elaboration` 回調，完成階層結構和連接埠綁定。

## 2. 模擬迴圈 (The "Crunch")
SystemC 的核心是 **評估-更新-通知 (Evaluate-Update-Notify)** 迴圈，在 `sc_simcontext::crunch()` 中實作。此迴圈確保了與 Verilog 的 delta cycles 類似的確定性行為。

### 2.1 階段 1：評估 (Evaluation) (The "Run" Phase)
-   **動作**: 核心遍歷 **可執行行程 (runnable processes)** 的清單。
-   **執行**:
    -   `SC_METHOD`: 直接呼叫 C++ 方法。它執行到完成後回傳。
    -   `SC_THREAD`: 核心切換 CPU 上下文 (堆疊指標) 到執行緒的堆疊。執行緒執行直到呼叫 `wait()`，這會將上下文切換回核心。
-   **結果**: 行程讀取目前的訊號值並請求新值 (例如 `sig.write(val)`)。關鍵在於，**基本通道 (primitive channels) 不會立即更新**。它們將新值儲存在臨時緩衝區中。

### 2.2 階段 2：更新 (Update) (The "Commit" Phase)
-   **動作**: 一旦可執行佇列為空，核心在 `prim_channel_registry` 上呼叫 `perform_update()`。
-   **執行**: 所有具有待處理值的基本通道 (訊號) 現在應用這些值。
-   **意義**: 這保證了評估階段內行程的執行順序不會改變其他行程讀取的值。在更新階段之前，每個人都看到 "舊" 值。

### 2.3 階段 3：通知 (Notification) (The "Trigger" Phase)
-   **動作**: 核心檢查產生的事件 (`sc_event::notify()`)。
-   **Delta 通知**: 如果訊號在更新期間發生變化，它會通知敏感行程。
-   **計時通知**: 核心檢查通用事件清單。
-   **結果**: 如果觸發了新行程 (變為可執行)，迴圈會回到 **階段 1** 而不推進模擬時間。這就是 **Delta 週期 (Delta Cycle)**。

### 2.4 時間推進
-   如果沒有可執行行程且沒有待處理的 delta 事件，引擎會尋找 **最近的未來計時事件**。
-   **動作**: 模擬時間 (`sc_time_stamp`) 推進到該時間戳記。等待的行程變為可執行，迴圈重新開始。

## 3. 硬體映射
| SystemC 階段 | Verilog 對應 | 概念 |
| :--- | :--- | :--- |
| **Evaluate** | Active Region | `always` 區塊執行 |
| **Update** | NBA Region | 非阻塞賦值 (`<=`) 更新 |
| **Delta Cycle** | Delta Delay | 在相同模擬時間的虛擬時間步 |
| **Time Advance** | `#delay` | 在模擬時間中向前移動 |

## 4. 關鍵檔案
-   `sysc/kernel/sc_simcontext.cpp`: 實作 `crunch()` 迴圈和時間管理。
-   `sysc/kernel/sc_main.cpp`: 啟動核心的橋接器。
-   `sysc/communication/sc_prim_channel.cpp`: 實作 `update()` 請求邏輯。
