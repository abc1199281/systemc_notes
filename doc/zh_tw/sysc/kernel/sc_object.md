# SystemC Object 分析

> **檔案**: `ref/systemc/src/sysc/kernel/sc_object.cpp`

## 1. 概述
`sc_object` 是 SystemC 中所有模擬物件 (channels, ports, modules 等) 的抽象基底類別。它提供：
- **識別 (Identification)**: 名稱、種類。
- **階層 (Hierarchy)**: 父子關係 (透過 `sc_object_host` 和 `sc_object_manager`)。
- **屬性 (Attributes)**: 附加任意元數據 (metadata) 的機制 (`sc_attr_base`)。

## 2. 核心概念

### 2.1 物件初始化 (`sc_object_init`)
當一個 `sc_object` 被建立時：
1.  **Simulation Context**: 它取得目前的 `sc_simcontext`。
2.  **Parenting**: 它找到它的父母 (父物件 `active_object()`) 並將自己加入到父母的子物件清單中。
3.  **Registration**: 它將自己插入 `sc_object_manager`。

### 2.2 階層管理 (Hierarchy Management)
物件階層在建構期間動態維護。
- `m_parent`: 指向父物件的指標。
- `get_child_objects()`: (通常透過 `sc_object_host` 繼承/管理)。

### 2.3 屬性 (Attributes)
SystemC 物件支援動態屬性系統。
- `add_attribute()`, `get_attribute()`: 允許使用者將自定義資料鍵/值 (key/value) 附加到任何物件，這對於工具特定的後端或追蹤非常有用。

---

## 3. 硬體 / RTL 對應
`sc_object` 本身通常對應到 netlist 中的 **元件實例 (Component Instances)** 或 **訊號名稱 (Signal Names)**。
- `sc_object` 的 `name()` (路徑) 對應到 Verilog/VHDL netlist 中的階層路徑 (例如 `top.cpu.alu.adder`)。

---

## 4. 關鍵重點
1.  **建構順序很重要**: SystemC 在 *執行建構子期間* 建立階層。"正在活動 (active)" 的物件 (目前正在執行其建構子的物件) 會成為任何新物件的父母。
2.  **唯一名稱**: 核心強制執行唯一性。如果你嘗試在同一個範圍內建立兩個具有相同名稱的物件，`sc_object_manager` (透過 `sc_name_gen`) 通常會產生一個唯一的後綴。
3.  **分離 (Detach)**: 物件可以被 "分離" (從階層中移除)，但在模擬期間這很少見且危險。
