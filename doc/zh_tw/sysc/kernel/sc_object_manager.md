# SystemC Object Manager 分析

> **檔案**: `ref/systemc/src/sysc/kernel/sc_object_manager.cpp`

## 1. 概述
`sc_object_manager` 是所有模擬物件的中心資料庫。它維護：
- **實例表 (Instance Table)**: 所有命名物件的 `active` 註冊表。
- **物件階層堆疊 (Object Hierarchy Stack)**: 在物件建構期間追蹤 "目前" 的父母。
- **模組名稱堆疊 (Module Name Stack)**: 追蹤 `sc_module_name` 物件以進行正確的模組建構。

## 2. 核心職責

### 2.1 階層追蹤 (`active_object`)
- **`hierarchy_push(sc_object_host*)`**: 將一個物件 (如模組) 推送到堆疊上。任何隨後建立的物件都會成為其子物件。
- **`hierarchy_pop()`**: 當物件範圍結束時 (例如，模組建構子結束) 彈出物件。
- **`active_object()`**: 返回堆疊頂部。

### 2.2 名稱管理 (`create_name`)
- 產生完整限定的階層名稱 (例如 `top.mod.sig`)。
- **衝突解決 (Collision Resolution)**: 如果名稱已存在，它會附加一個唯一的後綴 (透過 `sc_name_gen`) 並發出警告。
- **實例表**: 映射 `std::string` (name) -> `sc_object*`。

### 2.3 模組名稱堆疊 (Module Name Stack)
這個特定的堆疊用於解決 C++ 建構子的限制 (乾淨地傳遞名稱)。
- `push_module_name()` / `pop_module_name()`
- 由 `sc_module` 和 `sc_module_name` 使用來握手 (handshake) 名稱。

---

## 3. 關鍵重點
1.  **單一事實來源 (Single Source of Truth)**: 用於查找，如 `sc_find_object("top.mod.sig")`。
2.  **生命週期管理**: 可以移除/分離物件，雖然這很少見。
3.  **外部名稱**: 可以追蹤不是 SystemC 物件的 "外部" 名稱 (較少見)。
