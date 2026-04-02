# SystemC 學習筆記

[English](README.md) | [繁體中文]

> **SystemC 函式庫的結構化學習筆記** — 從由上而下的概念架構到由下而上的原始碼解析。

## 目標讀者

沒有硬體 RTL 背景的軟體工程師，想要深入理解 SystemC。所有解釋力求連高中生都能理解。

## 文件目錄

| 目錄 | 語言 | 說明 |
|------|------|------|
| [zh-TW/](zh-TW/) | 繁體中文 | 完整筆記：由上而下概念、由下而上原始碼解析、範例、圖表 |
| [en/](en/) | English | 由上而下概念、範例、圖表 |

### 內容結構

```
topdown/     — 概念性架構文件（模擬引擎、事件、排程等）
code/        — 逐檔原始碼解析（僅繁中版）
examples/    — SystemC 與 TLM 範例導讀
diagrams/    — 架構總覽圖表
```

## 建議閱讀順序

1. **先讀** [learning-path](zh-TW/topdown/learning-path.md)，了解建議的閱讀順序
2. 每份文件都以「生活類比」開頭，幫助建立直覺
3. 概念理解後，前往 `zh-TW/code/` 查看對應的底層程式碼文件
4. Mermaid 圖表可用 GitHub、VSCode 預覽或任何支援 Mermaid 的工具檢視

## 關於

本筆記用於系統性研讀 [Accellera SystemC](https://systemc.org/) 開源函式庫，涵蓋 SystemC 核心與 TLM（Transaction-Level Modeling）。
