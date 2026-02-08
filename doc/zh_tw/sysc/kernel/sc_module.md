# SystemC Module 分析

> **檔案**:
> - `ref/systemc/src/sysc/kernel/sc_module.cpp`
> - `ref/systemc/src/sysc/kernel/sc_module_name.cpp`

## 1. 概述
`sc_module` 是行程、連接埠、通道和其他模組的容器類別。它是結構化階層的基本建構區塊。

## 2. `sc_module` 生命週期

### 2.1 建構與 `sc_module_name`
模組的建構是一個精細的過程，以確保正確捕捉階層：
1.  **User**: `SC_MODULE(my_mod) { ... }` 展開為一個接受 `sc_module_name` 的建構子。
2.  **`sc_module_name` Constructor**:
    - 將自己推送到 `sc_object_manager` 中的堆疊。
    - 這 "標記" 了模組建構的開始。
3.  **`sc_module` Constructor**:
    - 查看 `sc_module_name` 堆疊以取得其名稱。
    - 將 *自己* 推送為 `sc_simcontext` 中的 **active object**。
    - 現在，任何建立的子物件 (ports, submodules) 都會將此模組視為其父母。
4.  **`sc_module_name` Destructor**:
    - 呼叫 `m_module_p->end_module()`。
    - 這觸發 `simcontext->hierarchy_pop()`，關閉範圍。

### 2.2 `end_module()`
明確關閉模組的階層範圍。在現代 SystemC (`SC_CTOR`) 中，這由 `sc_module_name` RAII 模式自動處理。

### 2.3 Process Declaration
`sc_module` 提供 `SC_METHOD`, `SC_THREAD`, `SC_CTHREAD` 的後端。
- `declare_method_process`
- `declare_thread_process`
- `declare_cthread_process`
這些函式建立行程 handle，將其註冊到核心，並設定初始敏感度。

### 2.4 Port Binding (`positional_bind`)
雖然命名綁定 (`port(channel)`) 是首選，但 `sc_module` 實作了位置綁定 (已廢棄的 `<<` 運算子或 `operator()`)。
- 它遍歷 `m_port_vec` (已註冊的 ports) 並依序綁定它們。

---

## 3. 硬體 / RTL 對應

| SystemC (`sc_module`) | Verilog / SystemVerilog |
| :--- | :--- |
| `sc_module` | `module ... endmodule` |
| `SC_CTOR` | `initial begin ... end` + 實例化邏輯 |
| `sc_in`, `sc_out` 成員 | Input/Output ports |
| Submodule 成員 | Module instances |

---

## 4. 關鍵重點
1.  **RAII 階層**: `sc_module_name` 的使用實際上是一個技巧，用來偵測建構子執行的 *開始* 和 *結束*，而無需使用者明確呼叫 `end_module()`。
2.  **行程容器**: Modules 是行程的 "宿主" (`sc_object_host`)。當行程執行時，其 `m_parent` 是它所在的模組。
3.  **生命週期回調**: `before_end_of_elaboration`, `end_of_elaboration`, `start_of_simulation` 是掛鉤在此處的虛擬方法，允許使用者在特定階段執行程式碼。
