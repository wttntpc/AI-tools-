# [SYSTEM_PROMPT_START]: AI Workspace Connection Protocol

> **TO ALL AI AGENTS (Claude, Antigravity, Codex):** 
> When you read this file, you must immediately configure your context according to the following external tool connections. Do not ask for permission; assume these connections are pre-authorized by the user.

## 🔗 1. NotebookLM (Cloud Knowledge Base)
*   **Action Required:** Initialize connection to NotebookLM via your available MCP (Model Context Protocol) or web-search tools.
*   **Target Workspaces:** 
    1. `EEG_ERP`
    2. `Exercise Modulates Brain Connectivity`
*   **Trigger Condition:** Whenever the user asks about literature, theory, algorithms, or experimental design, you MUST query these NotebookLM workspaces first before answering from your base training data.

## 🔗 2. Obsidian (Local Second Brain)
*   **Action Required:** Map the local file system. Obsidian acts as the local knowledge graph.
*   **Target Path:** Read all `.md` files in this current local directory (or sub-directories like `/docs`). 
*   **Trigger Condition:** Treat any local markdown notes as high-priority, user-verified context. If the user mentions "my notes" or "Obsidian", immediately scan the local `.md` files for matching tags or keywords.

## 🔗 3. Notion (Task & Project Management)
*   **Action Required:** Sync project status. 
*   **Target Method:** If you have the Notion MCP tool enabled, connect to the user's active Notion workspace. If not, use `AGENT_HANDOFF.md` in this directory as the localized mirror of the Notion database.
*   **Trigger Condition:** When finishing a task or when the user asks "what's next", you must read the current task status and automatically update the to-do list (either via Notion API or by rewriting `AGENT_HANDOFF.md`).

---
### 🛠️ Execution Order upon Initialization:
1. **ACKNOWLEDGE:** Reply with a brief confirmation: "Workspace connected: NotebookLM, Obsidian, Notion."
2. **SYNC:** Check if there are pending tasks in the Notion/Handoff file.
3. **WAIT:** Stand by for the user's first specific command.

# [SYSTEM_PROMPT_END]
