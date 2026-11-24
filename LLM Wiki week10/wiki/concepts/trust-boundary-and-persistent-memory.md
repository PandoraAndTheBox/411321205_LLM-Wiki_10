---
title: 信任邊界與持久性記憶（Trust Boundary & Persistent Memory）
type: concept
tags: [trust-boundary, persistent-memory, semantic-imitation, rag-vs-memory]
updated: 2026-11-24
---

# 信任邊界與持久性記憶（Trust Boundary & Persistent Memory）

## 信任邊界（Trust Boundary）的崩解原因
在傳統軟體安全中，「控制通道（指令）」與「資料通道（輸入）」通常有明確的語法或權限邊界。然而在 LLM 與 AI Agent 架構中，所有文字——無論是系統提示、使用者輸入、外部網頁、工具回傳值，或是歷史記憶——最終都會被拼接進同一個 Context Window 交由模型處理，導致信任邊界極易模糊：
- **外部資料變成間接指令**：若系統未明確區隔與標記外部來源（見 [OWASP LLM01](../sources/owasp-llm01-2025-prompt-injection.md) 與 [Palo Alto Networks](../sources/paloalto-ai-agent-attack-surface.md)），模型會將外部資料當作命令執行。
- **代理間的隱性信任**：在多代理架構中，一個 Agent 的輸出成為另一個 Agent 的輸入，若缺乏相互認證與任務驗證，信任邊界會在內部悄悄失效。

## 持久性記憶（Persistent Memory）如何改變威脅模型

根據 [Mem0 AI Memory Security 指南](../sources/mem0-ai-memory-security.md)，導入跨會話的持久性記憶後，系統狀態不再隨著對話結束而重置，記憶庫成為影響所有未來推理的**共享狀態層（Shared State Layer）**。

### RAG 與 Memory 的信任差異
雖然兩者都透過檢索補充上下文，但 Agent 對兩者的信任假設截然不同：
- **RAG（檢索增強生成）**：Agent 通常將檢索結果視為「外部參考資料（Reference Material）」。
- **Persistent Memory（持久性記憶）**：Agent 將檢索出的內容視為「自己過去的經驗或已驗證的偏好」。

### 語意模仿啟發式（Semantic Imitation Heuristic）
- **概念來源**：[MemoryGraft (Srivastava & He, 2025)](https://arxiv.org/abs/2512.16962) 提出此術語，指 Agent 傾向複製檢索到的「過去成功任務」中的行為模式（將記憶作為 Few-shot demonstrations 直接送入推理鏈）。
- **安全含義**：當這些「成功經驗」是攻擊者偽造並植入的紀錄時，Agent 會在沒有任何異常觸發詞的情況下，主動且自然地重現惡意行為——這使得 [Memory Poisoning](../threats/memory-poisoning.md) 比傳統 [Prompt Injection](../threats/prompt-injection.md) 更難被察覺。

## 跨來源關聯與防禦原則
- 因為記憶層兼具「持久性」與「高內部信任度」，[Mem0 指南](../sources/mem0-ai-memory-security.md) 主張必須把記憶庫當成正式生產資料庫來治理（隔離命名空間、加密雜湊、TTL 過期、複合信任評分），並納入 [縱深防禦](../defenses/defense-in-depth.md) 架構中。
- [OWASP LLM01](../sources/owasp-llm01-2025-prompt-injection.md) 則補充：在進行滲透測試與對抗演練時，應直接**把模型本身視為「不可信使用者（Untrusted User）」**，藉此檢驗系統的信任邊界與存取控制是否真正有效。
