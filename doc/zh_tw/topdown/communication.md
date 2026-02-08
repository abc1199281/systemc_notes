# 自頂向下分析：通訊與訊號

SystemC 將 **計算 (computation)** (行程) 與 **通訊 (communication)** (通道) 分離。這種分離允許模組獨立於用於連接它們的協定進行設計。

## 1. 介面-方法呼叫 (IMC) 模式
從本質上講，SystemC 通訊就是 C++ 方法呼叫。
-   **介面 (`sc_interface`)**: 定義 *合約* (純虛擬函式)。例如 `write()`, `read()`。
-   **通道 (`sc_prim_channel` / 階層式通道)**: 實作介面。例如 `sc_signal` 透過儲存值來實作 `write`。
-   **連接埠 (`sc_port`)**: 持有介面的指標。

**執行流程**:
1.  **行程**: 呼叫 `port->write(10)`。
2.  **連接埠**: 解引用其內部介面指標：`interface->write(10)`。
3.  **通道**: 執行 `write` 邏輯 (例如更新值)。

## 2. 基本通道與確定性
基本通道 (如 `sc_signal`, `sc_fifo`, `sc_mutex`) 實作 `sc_prim_channel` 基底類別。它們使用 **請求-更新 (Request-Update)** 模式來確保模擬確定性 (類似於 Verilog 的 NBA)。

### 2.1 請求階段 (Evaluate)
當行程呼叫 `signal.write(new_val)` 時：
-   訊號檢查 `new_val != current_val`。
-   如果不同，它將 `new_val` 儲存在 "下一個值" 緩衝區中。
-   它呼叫 `request_update()`，將通道註冊到 `sc_prim_channel_registry`。
-   **關鍵**: 在此 delta cycle 期間，`current_val` 保持不變！

### 2.2 更新階段
在所有行程都執行完畢 (評估階段結束) 後：
-   核心遍歷註冊表並在所有已註冊的通道上呼叫 `update()`。
-   `sc_signal::update()`: 將 "下一個值" 複製到 "當前值"。
-   如果值發生變化，它會建立一個 **Delta 通知** 事件。

### 2.3 通知階段
-   核心觸發 delta 通知事件。
-   對 `signal` 敏感的任何行程 (例如 `sensitive << signal`) 在 *下一個* delta cycle 變為可執行。

## 3. 連接埠綁定解析
綁定連接埠是將模組中的 `port` 連接到通用 `interface` (可能是通道或另一個連接埠) 的行為。
-   **細化時期**: 使用 `port(channel)` 或 `port.bind(channel)` 建立連接清單。
-   **解析**: 在 `end_of_elaboration` 時，SystemC "扁平化" 連接。即使你有 `連接埠 A -> 連接埠 B -> 連接埠 C -> 通道`，在解析後，`連接埠 A` 將 *直接* 指向 `通道`。這確保在模擬期間，通訊只是單一指標解引用 (零開銷)。

## 4. 硬體映射
| SystemC 概念 | Verilog 對應 |
| :--- | :--- |
| `sc_signal` | `wire` 或 `reg` |
| `sc_in`, `sc_out` | `input` / `output` 連接埠 |
| `write()` | 賦值 (`=`, `<=`) |
| `sc_fifo`, `sc_mutex` | 沒有直接的基本元件 (需要明確的行為程式碼) |
