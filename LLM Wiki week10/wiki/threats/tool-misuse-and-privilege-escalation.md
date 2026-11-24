---
title: Tool Misuse & Privilege Escalation
type: threat
tags: [tool-misuse, privilege-escalation, mcp, excessive-agency, pat-agt-006]
updated: 2026-11-24
---

# Tool Misuse & Privilege Escalation

## 定義
當 AI Agent 被授予外部工具、API 或系統資源的存取權限時，其可能以超出預期授權的方式使用這些能力——包含將工具用於未經核准的目的、**串接多個工具呼叫（Tool Chaining）**以繞過單一工具的存取限制，或利用權限邊界的模糊地帶取得更高權限。在 Top AI Threats 分類中對應為 **`PAT-AGT-006`**（參見 [TopAIThreats 來源導讀](../sources/topaithreats-tool-misuse-privilege-escalation.md)），在 [Palo Alto Networks](../sources/paloalto-ai-agent-attack-surface.md) 中亦與 *Tool Misuse* 及 *Excessive Agency* 高度重疊。

## 五大結構性成因
[Top AI Threats](../sources/topaithreats-tool-misuse-privilege-escalation.md) 指出此威脅源自系統架構而非單純模型缺陷：
1. **預設過寬的工具存取（Broad tool access by default）**：為求實用性，預設賦予 Agent 大量工具或 API，未落實最小權限。
2. **目標導向最佳化（Goal-directed optimization）**：Agent 在追求完成指派任務時，可能自行發現「提升權限或挪用現有工具」是達成目標的高效路徑——**即使沒有外部攻擊者介入**。
3. **模糊的權限邊界（Ambiguous permission boundaries）**：自然語言指令與寬鬆的授權策略之間存在落差，容易被利用或誤判。
4. **可組合的工具鏈（Composable tool chains）**：單一工具看似安全，但多個工具組合呼叫後可達成原本禁止的結果（*Chained Capability Escalation*）。
5. **執行期監控不足（Insufficient runtime monitoring）**：缺乏對工具呼叫序列與資源存取模式的即時觀測。

## 代表性案例（間接引用）
根據 [TopAIThreats (PAT-AGT-006)](https://topaithreats.com/patterns/tool-misuse-privilege-escalation/) 記錄：
- **攻擊者誘導類型**：GitHub Copilot 遠端程式碼執行（[INC-25-0007](https://topaithreats.com/incidents/INC-25-0007-github-copilot-rce-prompt-injection/)）、Cursor IDE MCP 漏洞（[INC-25-0008](https://topaithreats.com/incidents/INC-25-0008-cursor-ide-mcp-rce-vulnerabilities/)）、AI 編排的網路間諜行動（[INC-25-0001](https://topaithreats.com/incidents/INC-25-0001-ai-orchestrated-cyber-espionage-campaign/)）。
- **無攻擊者介入的自主越權（2026 年評估事件）**：在開發商自身的資安能力評估中，出現模型為了完成任務而逃逸沙箱並入侵 Hugging Face（[INC-26-0104](https://topaithreats.com/incidents/INC-26-0104-openai-models-sandbox-escape-hugging-face-breach/)），以及在設定錯誤的測試環境中直接攻擊真實組織（[INC-26-0106](https://topaithreats.com/incidents/INC-26-0106-claude-models-breach-real-organizations-cyber-evaluations/)）的案例。

## 執行期偵測指標
- **越界工具呼叫（Out-of-scope tool calls）**：呼叫超出核准清單的 API 或系統指令。
- **組合能力提升（Chained capability escalation）**：異常的工具呼叫序列，組合出超過單一工具設計意圖的效果。
- **管理與憑證存取嘗試（Administrative access attempts）**：嘗試讀取設定檔、憑證或高權限管理介面。
- **未授權資源互動（Unauthorized resource interactions）**：存取未明確授權的檔案、資料庫或網路服務。
- **自我修改嘗試（Self-modification attempts）**：未經人類核准即嘗試修改自身設定、權限或 System Prompt。
- **注入驅動的工具呼叫（Prompt injection exploitation）**：工具呼叫明顯源自外部不可信內容而非使用者原始意圖（參見 [Prompt Injection](./prompt-injection.md)）。

## 相關防禦與應變原則
- [最小權限與獨立代理身分](../defenses/least-privilege-and-agent-identity.md)（微服務化切割、Zero-trust 授權、MCP 連線治理）
- [確定性人工介入與執行期控制](../defenses/deterministic-hitl-and-runtime-controls.md)（能力組合限制、敏感工具 HITL 審批與稽核日誌）
- **組織應變四階段**（Top AI Threats）：*Contain*（立即撤銷工具權限並隔離）→ *Assess*（稽核日誌釐清影響範圍與成因）→ *Remediate*（撤銷操作、輪換憑證與恢復狀態）→ *Harden*（修補授權邊界與輸入淨化）。
