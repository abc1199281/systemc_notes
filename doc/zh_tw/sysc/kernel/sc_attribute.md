# SystemC Attribute 分析

> **檔案**: `ref/systemc/src/sysc/kernel/sc_attribute.cpp`

## 1. 概述
屬性系統 (attribute system) 允許使用者將任意名稱-值對 (name-value pairs) (或複雜物件) 附加到任何 `sc_object`。這通常被 EDA 工具用來儲存元資訊 (meta-information) (例如合成約束、佈局資料)，而無需修改核心 SystemC。

## 2. 核心類別

### 2.1 `sc_attr_base`
所有屬性的基底類別。它本質上只持有名稱 (`std::string`)。使用者通常從 `sc_attribute<T>` (定義在 header 中) 繼承，它增加了數值儲存。

### 2.2 `sc_attr_cltn`
屬性的集合 (vector)。
- **唯一性 (Uniqueness)**: 確保集合中沒有兩個屬性具有相同的名稱。
- **查找 (Lookup)**: 提供 `operator[]` 來透過名稱尋找屬性。

## 3. 在 `sc_object` 中的使用
每個 `sc_object` 包含一個指向 `sc_attr_cltn` 的指標。
- `add_attribute()`
- `get_attribute()`

---

## 4. 關鍵重點
1.  **選擇性儲存 (Optional Storage)**: `sc_object` 中的 `sc_attr_cltn` 指標是延遲初始化 (lazily initialized) (僅在第一個屬性加入時)，以節省絕大多數不使用屬性的物件的記憶體。
