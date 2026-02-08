# SystemC Sensitivity 分析

> **檔案**: `ref/systemc/src/sysc/kernel/sc_sensitive.cpp`

## 1. 概述
處理模組建構子中使用的 **靜態敏感度 (static sensitivity)** 語法 (例如 `sensitive << clk.pos();`)。它允許行程在模擬開始 *之前* 宣告什麼事件會觸發它們。

## 2. `sc_sensitive` 物件
每個 `sc_module` 都有成員 `sensitive`, `sensitive_pos`, 和 `sensitive_neg`。
- 這些是 `sc_sensitive`, `sc_sensitive_pos` 等類型的物件。
- 它們維護一個 **current mode** (`SC_METHOD`, `SC_THREAD`) 和一個 **current handle** (正在建立的行程)。

### 2.1 運算子 `<<`
魔法發生在 `operator<<` 中。
1.  **切換上下文 (Switching Context)**: `sensitive << process_handle` 切換 `m_handle` 成員指向該行程。
2.  **加入敏感度 (Adding Sensitivity)**: `sensitive << event` (或 port/interface) 呼叫 `m_handle->add_static_event(e)`。

## 3. 變體 (Variants)
- **`sc_sensitive`**: 一般事件 (數值改變)。
- **`sc_sensitive_pos`**: 正緣事件 (`posedge_event()`)。
- **`sc_sensitive_neg`**: 負緣事件 (`negedge_event()`)。

## 4. 廢棄 (Deprecation)
此檔案透過警告並委派給 `operator<<` 來處理傳統語法 (函式呼叫運算子 `()`)。

---

## 5. 關鍵重點
1.  **僅建構時間 (Construction Time Only)**: 靜態敏感度是在建構/elaboration 期間定義的。一旦模擬開始 (`sc_is_running()`)，嘗試使用這些運算子會觸發錯誤。
2.  **Event Finders**: 對於 ports，它通常使用 `sc_event_finder` 來延遲實際的事件查找，直到 port 被綁定 (類似於 reset finder 邏輯)。
