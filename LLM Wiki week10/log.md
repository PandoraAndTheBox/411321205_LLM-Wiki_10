# Wiki Log

本檔案為唯讀追加（append-only）的時間軸紀錄，記錄每次知識庫的 Ingest、Query 沉澱與 Lint 檢查。

每筆紀錄開頭固定使用 `## [YYYY-MM-DD] <operation> | <title>` 格式。

---

## [2026-11-24] init | Initialize AI Agent Security LLM Wiki
- 建立 `AGENTS.md` 定義讀書會導向的知識庫規範與作業流程。
- 初始化 `index.md`、`log.md` 與 `wiki/` 目錄結構（`sources/`、`threats/`、`defenses/`、`concepts/`、`synthesis/`）。

## [2026-11-24] ingest | Batch ingest 5 initial AI Agent Security sources
- **收錄來源（5 篇）**：`mem0-ai-memory-security.md`、`microsoft-defense-in-depth-agents.md`、`owasp-llm01-2025-prompt-injection.md`、`topaithreats-tool-misuse-privilege-escalation.md`、`paloalto-ai-agent-attack-surface.md`。
- **新增主題頁（9 篇）**：
  - `threats/`：`prompt-injection.md`、`memory-poisoning.md`、`tool-misuse-and-privilege-escalation.md`。
  - `defenses/`：`defense-in-depth.md`、`least-privilege-and-agent-identity.md`、`deterministic-hitl-and-runtime-controls.md`。
  - `concepts/`：`agentic-attack-surface.md`、`trust-boundary-and-persistent-memory.md`。
  - `synthesis/`：`ai-agent-security-landscape.md`。
- **備註**：已過濾 `Title What Is the AI Agent Attack S.txt` 前後端導覽／Cookie 雜訊與各篇商業產品推銷；文內提及但 `raw/` 尚無全文之 arXiv 論文與 INC 資安事件已依規範作為案例簡述並保留外部 URL。
