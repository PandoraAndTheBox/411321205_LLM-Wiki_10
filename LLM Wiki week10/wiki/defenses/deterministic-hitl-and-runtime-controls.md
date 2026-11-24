---
title: 確定性人工介入與執行期控制（Deterministic HITL & Runtime Controls）
type: defense
tags: [hitl, runtime-control, input-output-validation, ttl, rag-triad]
updated: 2026-11-24
---

# 確定性人工介入與執行期控制（Deterministic HITL & Runtime Controls）

## 核心理念
當系統無法保證機率性模型不犯錯或不被誘導時，必須在**執行期（Runtime）**建立確定性的檢查閘門，確保高風險行為不會在未經審查或驗證的情況下落地。

## 1. 確定性 Human-in-the-Loop（HITL）治理
- **常見設計誤區**：[Microsoft 安全指南](../sources/microsoft-defense-in-depth-agents.md) 指出，最致命的錯誤是「讓模型自行判斷何時需要人類審核」。若升級條件依賴機率性推理，對抗性提示或模糊指令就能誘導模型「說服自己不需要人工審核」。
- **正確架構原則**：
  - HITL 觸發條件必須由**應用程式層或編排層（Orchestrator）在程式碼中確定性強制執行**，將「推理」與「執行授權」徹底分離。
  - 介入點應能發生在**執行過程中（包含工具呼叫當下）**，而不僅限於任務開始前或結束後。
  - 高影響或不可逆操作（如刪除資料、變更權限、執行程式碼、金融交易、對外發送訊息）應強制要求人工核准（[OWASP LLM01](../sources/owasp-llm01-2025-prompt-injection.md)、[TopAIThreats](../sources/topaithreats-tool-misuse-privilege-escalation.md)、[Palo Alto Networks](../sources/paloalto-ai-agent-attack-surface.md)）。

## 2. 輸入區隔、輸出驗證與能力組合限制
- **區隔不可信內容**：將系統指令與外部資料（網頁、郵件、RAG 文件、工具回傳）明確分離並標記，防止不可信內容直接竄改目標（[OWASP LLM01](../sources/owasp-llm01-2025-prompt-injection.md)）。
- **確定性輸出格式與語意驗證**：強制要求結構化輸出並以確定性程式碼驗證格式；在語意層面可結合 **RAG Triad**（評估 Context Relevance、Groundedness 與 Question/Answer Relevance）偵測異常或偏離事實的輸出。
- **能力組合限制（Capability Composition Limits）**：[TopAIThreats](../sources/topaithreats-tool-misuse-privilege-escalation.md) 強調，執行期監控不能只檢查單一工具呼叫是否合法，還必須偵測「異常的工具呼叫序列」是否組合出超越原始授權的效果。

## 3. 記憶層的執行期衛生與時間邊界
針對 [Memory Poisoning](../threats/memory-poisoning.md)，[Mem0 安全指南](../sources/mem0-ai-memory-security.md) 歸納出幾項關鍵執行期機制：
- **複合信任評分（Composite Trust Scoring）**：單一偵測器容易漏檢（A-MemGuard 漏檢率 66%），[Sunil et al. (2026)](https://arxiv.org/abs/2601.05504) 建議結合時間訊號、模式過濾與內容分析計算複合信任分數，並在檢索時對舊條目套用時間衰減（Temporal Decay）。
- **TTL（Time-to-Live）與鑑識快照（Forensic Snapshots）**：為記憶設定有效期限，能將未被偵測到的中毒記憶影響窗口限制在有限時間內；搭配時間點快照，可在發現異常時直接回滾至已知安全狀態。
- **全流程可觀測性與紅隊演練**：記錄提示、工具呼叫、記憶存取與代理間通訊日誌，並定期以完整工作流為單位進行對抗測試（把模型本身視為不可信使用者進行測試）。

## 權衡與限制
- **過度過濾的可用性代價**：[Sunil et al. (2026)](https://arxiv.org/abs/2601.05504) 指出記憶淨化閾值極難校準——過於嚴格會誤殺合法記憶、降低 Agent 效能；過於寬鬆則讓隱蔽攻擊穿透，需依業務風險容忍度動態調整。
- **HITL 的自動化阻力**：過於頻繁的人工審批會抵銷 Agent 的自動化價值與使用者體驗，因此觸發條件應依據操作的「可逆性」與「影響半徑」分級設計。
