---
title: AI Agent 攻擊面（Agentic Attack Surface）
type: concept
tags: [attack-surface, mcp, multi-agent, autonomy]
updated: 2026-11-24
---

# AI Agent 攻擊面（Agentic Attack Surface）

## 定義
*AI Agent Attack Surface* 是指 AI Agent 在執行多步驟自主任務的過程中，所有可能被暴露、濫用或遭到攻擊的系統、身分、工具、資料來源、記憶儲存與通訊路徑的總和（參見 [Palo Alto Networks 來源導讀](../sources/paloalto-ai-agent-attack-surface.md)）。

其核心轉變在於：**行動能力創造了真實後果**。當系統僅生成文字時，錯誤大多停留在輸出內容；當 Agent 具備行動權限時，一次模型錯誤或被操縱的指令就能直接修改外部資料、觸發工作流或入侵相連系統。

## AI Agent 與傳統 LLM 應用的攻擊面差異

根據 [Palo Alto Networks](../sources/paloalto-ai-agent-attack-surface.md) 的架構對比（兩者邊界並非絕對，應依實際能力與權限評估）：

| 評估維度 | 傳統 LLM 應用 | AI Agent |
| :--- | :--- | :--- |
| **主要功能** | 生成或轉換內容 | 透過多步驟行動達成目標 |
| **外部存取** | 可能使用檢索（RAG）或單一服務 API | 常自主選擇並串接呼叫多個工具 |
| **身分設計** | 多在單一應用程式會話內運作 | 需獨立代理身分或委派憑證 |
| **狀態管理** | 保留對話歷史或應用端管理之上下文 | 使用工作狀態與跨會話持久性記憶指導行動 |
| **營運影響** | 多限於文字內容或介面輸出 | 可修改資料、觸發流程或操作相連系統 |
| **通訊模式** | 通常僅與使用者或應用元件互動 | 可能與其他 Agent 及編排器（Orchestrator）協作 |

## 構成 AI Agent 攻擊面的八大層面
1. **指令、推理與規劃（Instructions, reasoning & planning）**：目標拆解與規劃過程可能遭 [Prompt Injection](../threats/prompt-injection.md) 劫持。
2. **工具與外部系統（Tools & external systems）**：API、資料庫、SaaS、瀏覽器、程式碼執行環境與檔案系統，容易成為 [Tool Misuse & Privilege Escalation](../threats/tool-misuse-and-privilege-escalation.md) 的目標。
3. **MCP 連線（Model Context Protocol connections）**：標準化工具連線若缺乏企業級治理、伺服器驗證與權限審查，可能引入惡意 Server、過度授權或 Token 濫用。
4. **身分與憑證（Identity & credentials）**：共用帳號或過度寬鬆的服務憑證會導致橫向移動與 *Credential Compromise*。
5. **資料與上下文來源（Data & context sources）**：郵件、網頁、文件與檢索系統中的不可信內容，成為間接注入通道或資料外洩路徑。
6. **記憶與儲存狀態（Memory & stored state）**：短期工作狀態與長期記憶可能遭受 [Memory Poisoning](../threats/memory-poisoning.md) 或保留不該儲存的敏感資訊。
7. **代理間通訊（Inter-agent communication）**：多代理系統中的隱性信任關係，若其中一個節點遭攻陷或發生 *Agent Impersonation*，可能污染整個任務鏈。
8. **編排與執行期基礎設施（Orchestration & runtime infrastructure）**：負責協調模型、工具、記憶與策略的核心層，若防護失靈將一次波及所有元件。

## 相關防禦視角
- 縮減攻擊面的核心策略：盤點資產 → 獨立身分 → 限制工具與權限 → 視外部資料為不可信 → 高風險審批 → 端到端測試 → 執行期監控 → MCP 治理（見 [最小權限與獨立代理身分](../defenses/least-privilege-and-agent-identity.md) 與 [縱深防禦](../defenses/defense-in-depth.md)）。
