# 自頂向下分析：結構階層與細化 (Elaboration)

SystemC 模型是階層式結構的，類似於 VHDL 或 Verilog，但在執行時期使用 C++ 動態建構。

## 1. 細化：建構階段
與編譯式的 HDL 不同，SystemC "編譯" 成一個 C++ 可執行檔。硬體結構 (模組、連接埠、訊號) 在 **細化階段 (Elaboration Phase)** 期間建立，該階段從 `sc_main()` 開始執行直到呼叫 `sc_start()` 為止。

### 1.1 模組 (`sc_module`)
`sc_module` 是容器類別。
-   **註冊**: 當實例化一個模組時，它會向 `sc_simcontext` 註冊自己。
-   **階層堆疊 (Hierarchy Stack)**: `sc_object_manager` 維護一個 "活躍" 物件的堆疊。
    1.  當 `TopLevel` 建構子開始時，它將自己推送到堆疊上。
    2.  當 `SubModule` 在 `TopLevel` 內實例化時，`SubModule` 將 `TopLevel` 視為其父級。
    3.  當 `SubModule` 建構子結束時，它將自己彈出。
    4.  當 `TopLevel` 建構子結束時，它將自己彈出。

### 1.2 名稱機制 (`sc_module_name`)
C++ 建構子不知道正在建立的變數名稱。SystemC 使用 `sc_module_name` 的巧妙技巧：
```cpp
SC_MODULE(my_mod) {
    SC_CTOR(my_mod) { ... }
};
// 實例化:
my_mod m1("instance_name");
```
-   `sc_module_name("instance_name")` 物件在 `my_mod` 建構子主體 **之前** 建立。
-   它將名稱推送到核心中的特殊堆疊上。
-   `sc_module` 建構子彈出此名稱以將其分配給模組實例。
-   這確保每個物件都有一個字串名稱，用於報告和波形追蹤。

## 2. 物件註冊表
`sc_simcontext` 擁有一個 `sc_object_manager`，它追蹤：
-   **物件階層**: 父子關係 (專案 -> 頂層 -> 子模組 -> 訊號)。
-   **搜尋**: 像 `sc_find_object("top.sub.sig")` 這樣的函式遍歷此註冊表。
-   **唯一性**: 它強制在範圍內使用唯一名稱，如有必要會重新命名重複項 (例如 `sig`, `sig_0`, `sig_1`) 以防止衝突。

## 3. 模擬階段
SystemC 定義了用於管理階層的精確階段 (回調)：

1.  **建構 (Construction)**: 執行 C++ 建構子。建立基本樹。
2.  **`before_end_of_elaboration()`**: 使用者回調。最後的機會來實例化模組或更改屬性。
3.  **`end_of_elaboration()`**: 使用者回調。階層被凍結。
    -   **最佳化**: 連接埠綁定被定案。
    -   **驗證**: 檢查未綁定的連接埠。
4.  **`start_of_simulation()`**: 使用者回調。開啟檔案，列印標頭。
5.  **模擬迴圈**: 執行 `crunch()`。
6.  **`end_of_simulation()`**: 使用者回調。清理。

## 4. 硬體映射
| SystemC 概念 | Verilog 對應 |
| :--- | :--- |
| `sc_module` | `module ... endmodule` |
| `new Module("name")` | `Module name();` (實例化) |
| `end_of_elaboration` | 時間 0 初始化 (概念上) |
| `sc_main` | 頂層測試平台檔案 |
