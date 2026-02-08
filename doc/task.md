# Context
- 上次用另一款 AI 在 doc/formal/ 生成的文件顆粒度不夠，且沒有一對一對應 ref/systemc/src
- 每告一個段落（例如一個phase), please commit once and push to remote

## Naming rule
- Bottom-up: `ref/systemc/src/sysc/kernel/sc_event.cpp` → `doc/code/sysc/kernel/sc_event.md` (保留目錄結構)
- Top-down: `doc/topdown/{topic}.md`

## Task 0: 評估現有文件 (需要 user review)
- [ ] 列出 doc/formal/ 下所有 .md 檔案
- [ ] 產出評估報告到 doc/formal/evaluation.md
- [ ] **STOP: 等待 user 確認後再進行 Task 1**

## Task 1: 規劃 Bottom-Up 分析計劃
- [ ] 列出 ref/systemc/src 下所有檔案清單
- [ ] 建立 doc/code/plan.md，規劃分析順序與優先級
- [ ] 每批次完成後 commit 並繼續

## Task 2: 規劃 Top-Down 分析計劃  
- [ ] 建立 doc/topdown/plan.md
- [ ] 定義檔案之間的依賴關係分析方法
- [ ] 每批次完成後 commit 並繼續

## Task 3: 執行分析 (分批進行)
- [ ] 按照 doc/code/plan.md 或 doc/topdown/plan.md 的順序執行
- [ ] 每次分析最多 3 個檔案
- [ ] 每批次完成後 commit 並繼續