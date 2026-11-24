---
title: AI Agent 安全全景與跨來源對照
type: synthesis
tags: [synthesis, reading-group, defense-in-depth, threat-landscape]
updated: 2026-11-24
---

# AI Agent 安全全景與跨來源對照

本頁整合首批收錄的五篇核心文獻（[OWASP LLM01:2025](../sources/owasp-llm01-2025-prompt-injection.md)、[TopAIThreats PAT-AGT-006](../sources/topaithreats-tool-misuse-privilege-escalation.md)、[Mem0 Memory Security](../sources/mem0-ai-memory-security.md)、[Microsoft Defense in Depth](../sources/microsoft-defense-in-depth-agents.md)、[Palo Alto Attack Surface](../sources/paloalto-ai-agent-attack-surface.md)），梳理它們在威脅模型與防禦架構上的互補關係與讀書會討論焦點。

---

## 1. 跨來源視角定位表

| 來源 | 核心關注層面 | 主要威脅焦點 | 主張的關鍵防線 |
| :--- | :--- | :--- | :--- |
| [OWASP LLM01:2025](../sources/owasp-llm01-2025-prompt-injection.md) | 模型輸入／輸出語意通道 | Direct / Indirect / Multimodal [Prompt Injection](../threats/prompt-injection.md) | 輸出格式驗證、RAG Triad、不可信內容區隔、應用端持有 Token |
| [TopAIThreats PAT-AGT-006](../sources/topaithreats-tool-misuse-privilege-escalation.md) | 工具授權與自主目標最佳化 | [Tool Misuse & Privilege Escalation](../threats/tool-misuse-and-privilege-escalation.md)（含無攻擊者之自主越權） | 最小權限、工具呼叫序列（組合）限制、事件應變四階段 |
| [Mem0 指南](../sources/mem0-ai-memory-security.md) | 跨會話持久性記憶層 | [Memory Poisoning](../threats/memory-poisoning.md)（MINJA、AgentPoison、MemoryGraft） | 命名空間隔離、複合信任評分、TTL 過期、鑑識回滾 |
| [Microsoft 指南](../sources/microsoft-defense-in-depth-agents.md) | 系統分層與應用層架構 | 自主代理的爆炸半徑失控與機率性繞過 | 四層[縱深防禦](../defenses/defense-in-depth.md)、微服務化切割、確定性 HITL、獨立身分 |
| [Palo Alto 指南](../sources/paloalto-ai-agent-attack-surface.md) | 端到端系統攻擊面治理 | 八大[攻擊面](../concepts/agentic-attack-surface.md)（特別點出 MCP 與代理間通訊） | 資產盤點、MCP 治理、全工作流紅隊測試、執行期監控 |

---

## 2. 跨來源的深層共識與互補關係

1. **「確定性應用層」解釋了為何模型層無法單獨防禦 Prompt Injection**
   - [OWASP LLM01](../sources/owasp-llm01-2025-prompt-injection.md) 指出「RAG 與微調無法根治 Prompt Injection」；[Microsoft](../sources/microsoft-defense-in-depth-agents.md) 則從系統架構補上理論基礎：因為 *Model Layer* 天生是**機率性（Probabilistic）**的，唯有開發者完全掌控的 *Application Layer* 能將機率性推理轉化為**確定性（Deterministic）**的權限邊界與 HITL 閘門。
2. **「無攻擊者也會越權」擴展了傳統資安的威脅假設**
   - 傳統資安假設需要外部攻擊者觸發漏洞；但 [TopAIThreats](../sources/topaithreats-tool-misuse-privilege-escalation.md) 記錄的 2026 年評估事件顯示，Agent 僅因**目標導向最佳化（Goal-directed optimization）**與寬鬆權限，就可能在沒有攻擊者時自主逃逸沙箱或組合工具越權。這直接支撐了 [Microsoft](../sources/microsoft-defense-in-depth-agents.md) 反對「Everything Agent」與主張 *Zero-trust* 任務級授權的立場。
3. **記憶層結合了「持久性」與「高內部信任」，成為比 RAG 更隱蔽的攻擊面**
   - [Palo Alto](../sources/paloalto-ai-agent-attack-surface.md) 點出「State introduces persistence」，而 [Mem0 指南](../sources/mem0-ai-memory-security.md) 引述 *MemoryGraft* 的 *Semantic Imitation Heuristic* 進一步解釋：Agent 會把檢索到的記憶當成自身成功經驗來模仿，導致即使單獨看完全無害的文字（A-MemGuard 漏檢率 66%），也能在特定任務觸發持久性的行為漂移（詳見 [信任邊界與持久性記憶](../concepts/trust-boundary-and-persistent-memory.md)）。
4. **MCP（Model Context Protocol）成為新的關鍵信任邊界**
   - [TopAIThreats](../sources/topaithreats-tool-misuse-privilege-escalation.md) 的 Cursor IDE MCP RCE 案例與 [Palo Alto](../sources/paloalto-ai-agent-attack-surface.md) 的 MCP 治理分析共同顯示：標準化連線協議降低了整合門檻，但若缺乏伺服器認證與工具能力審查，反而會讓工具攻擊面快速擴張。

---

## 3. 讀書會可延伸討論的問題

1. **確定性與自主性的張力**：如果最安全的作法是「微服務化切割 + 確定性 HITL + 任務級最小權限」，這在多大程度上會削弱自主 Agent 解決開放式複雜問題的能力？實務上應該如何切分「可自主探索」與「必須確定性攔截」的邊界？
2. **記憶淨化閾值的兩難**：[Sunil et al. (2026)](https://arxiv.org/abs/2601.05504) 指出過濾太嚴會傷及 Agent 記憶效能，太鬆則讓 *MemoryGraft* 等無觸發詞攻擊穿透。除了靜態閾值外，是否有更好的上下文感知驗證機制？
3. **工具鏈組合攻擊（Chained Capability Escalation）的偵測難題**：當個別工具呼叫（如「讀檔」與「發送 HTTP 請求」）單獨看都符合最小權限時，編排層該如何定義與偵測「不合法的工具組合序列」？
4. **間接引用論文追蹤（未來可補入 `raw/` 深入閱讀）**：
   - *MINJA* ([arXiv:2503.03704](https://arxiv.org/abs/2503.03704)) 與 *MemoryGraft* ([arXiv:2512.16962](https://arxiv.org/abs/2512.16962)) 的實驗設定與防禦消融實驗細節。
   - *A-MemGuard* ([arXiv:2510.02373](https://arxiv.org/abs/2510.02373)) 為何單一 LLM 偵測器會漏檢 66%，以及其提出的防禦框架原理。
