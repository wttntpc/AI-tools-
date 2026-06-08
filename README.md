# 🤖 AI_CrossProject: 多代理人自動化協作工作區

歡迎來到本專案。這是一個專為「多 AI 代理人 (Multi-Agent) 接力協作」打造的開發與分析環境。本工作區旨在整合底層開發執行力與高層次數據分析能力，實現無縫的自動化工作流。

## 🎯 核心運作邏輯
本專案由不同專長的 AI Agent 共同維護：
*   **Claude Code (CLI):** 負責本機端程式碼撰寫、環境建置、自動化測試與除錯。
*   **Antigravity / Hermes (GUI):** 負責文獻檢索、高階架構規劃與數據視覺化解讀。

## 📂 核心系統檔案導覽
為了讓所有 AI Agent 能無縫接軌，請參閱以下核心指南檔：

1.  **[AGENT_HANDOFF.md](./AGENT_HANDOFF.md)**
    *   **用途：** 專案的「共用白板」。記錄當前任務進度、待辦事項與 Bug 追蹤。所有 Agent 上下班前必讀/必填。
2.  **[TOOLS_INTEGRATION.md](./TOOLS_INTEGRATION.md)**
    *   **用途：** 外部知識庫與工具 (NotebookLM, Obsidian, Notion) 的串接狀態與調用規範。
3.  **[CLAUDE.md](./CLAUDE.md)**
    *   **用途：** 專給終端機 Claude Code 讀取的最高行為準則與底層限制。

---
*💡 **致所有 AI Agent 的提示：** 在開始執行任何動作前，請優先閱讀 `AGENT_HANDOFF.md` 以獲取最新上下文。*
