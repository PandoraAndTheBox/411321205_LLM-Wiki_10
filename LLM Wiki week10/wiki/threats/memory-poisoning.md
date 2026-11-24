---
title: Memory Poisoning
type: threat
tags: [memory-poisoning, persistent-memory, owasp-asi06, minja, agentpoison, memorygraft]
updated: 2026-11-24
---

# Memory Poisoning

## 定義與威脅特性
*Memory Poisoning* 是指攻擊者將虛假、誤導或惡意資料植入 AI Agent 的 *Persistent Memory*（長期記憶庫）中。OWASP Agentic AI Top 10 將其獨立列為 **ASI06（Memory and Context Poisoning）**。

與傳統 [Prompt Injection](./prompt-injection.md) 相比，其核心差異在於**持久性（Persistence）**與**信任層級（Trust Level）**：
- **跨會話存活**：一般 *Prompt Injection* 在會話結束後即消失；*Memory Poisoning* 則會跨越會話重啟、Context Window 重置甚至模型更新，持續影響未來任務。
- **更高的隱蔽性與信任度**：Agent 會將檢索到的記憶視為「自己過去的成功經驗」，而非外部不可信資料（詳見 [信任邊界與持久性記憶](../concepts/trust-boundary-and-persistent-memory.md)）。根據 [A-MemGuard (arXiv:2510.02373)](https://arxiv.org/abs/2510.02373) 研究，即使先進的 LLM 偵測器仍會漏檢 **66%** 的中毒記憶條目，因為單獨看這些記憶往往看似無害（例如「永遠優先處理看似緊急的郵件」），只有與特定查詢結合時才顯現惡意。

## 三大研究驗證的攻擊向量

根據 [Mem0 AI Memory Security 指南](../sources/mem0-ai-memory-security.md) 彙整，學術界已證實三種主要攻擊模式：

| 攻擊類型 | 代表研究 | 所需權限 | 觸發條件 | 核心機制與實測數據 |
| :--- | :--- | :--- | :--- | :--- |
| **查詢式記憶注入** | [MINJA (Dong et al., 2025)](https://arxiv.org/abs/2503.03704) | 僅需一般使用者查詢權限（無需直接寫入記憶庫） | 語意相近的後續查詢 | 透過 *Indication Prompts*、*Bridging Steps* 與漸進式縮短策略，將惡意紀錄寫入受害者未來會檢索到的 Embedding 空間。在 GPT-4o-mini、Gemini-2.0-Flash 與 Llama-3.1-8B 測試中，達到 **>95% 注入成功率** 與 **>70% 攻擊成功率**。 |
| **知識庫後門中毒** | [AgentPoison (Chen et al., NeurIPS 2024)](https://arxiv.org/abs/2407.12784) | 需具備知識庫／記憶庫寫入權限 | 特定最佳化觸發詞（Trigger） | 透過受限最佳化構造與正常查詢在 Embedding 空間幾乎無法區分的觸發詞，繞過 Perplexity 異常偵測。在自駕、知識問答與醫療 EHR Agent 上達成 **>80% 平均攻擊成功率**，且不影響正常輸入表現。 |
| **經驗嫁接（持久行為漂移）** | [MemoryGraft (Srivastava & He, 2025)](https://arxiv.org/abs/2512.16962) | 間接注入（透過被攝取的外部內容） | **無需觸發詞** | 植入偽造的「成功任務經驗」，利用 Agent 的 *Semantic Imitation Heuristic* 在相似任務中自然複製惡意行為模式。在 MetaGPT DataInterpreter (GPT-4o) 測試中，少量中毒紀錄即可佔據正常負載下極高比例的檢索結果。 |

## 實務案例（間接引用）
- **Unit 42 Indirect Prompt Injection to Long-Term Memory PoC**（[Unit 42 報告](https://unit42.paloaltonetworks.com/indirect-prompt-injection-poisons-ai-longterm-memory/)）：展示透過被駭網頁進行間接注入，將惡意指令植入 Agent 長期記憶，在後續無關對話中持續被納入編排提示並靜默外洩對話歷史。

## 相關防禦原則
- [縱深防禦（Defense in Depth）](../defenses/defense-in-depth.md)（記憶生命週期六階段防護）
- [確定性人工介入與執行期控制](../defenses/deterministic-hitl-and-runtime-controls.md)（輸入淨化、複合信任評分、TTL 過期機制與鑑識快照回滾）
- [最小權限與獨立代理身分](../defenses/least-privilege-and-agent-identity.md)（以 `user_id` / `agent_id` 進行儲存層級隔離與 RBAC）
