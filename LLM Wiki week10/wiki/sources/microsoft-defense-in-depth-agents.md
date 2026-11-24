---
title: Defense in depth for autonomous AI agents
type: source
tags: [defense-in-depth, architecture, least-privilege, hitl, agent-identity]
updated: 2026-11-24
source_file: raw/Title Defense in depth for autonomo.txt
source_url: https://www.microsoft.com/en-us/security/blog/2026/05/14/defense-in-depth-autonomous-ai-agents/
published: 2026-05-14
---

# Defense in depth for autonomous AI agents

> **一句話核心主張**：當 AI Agent 從輔助生成走向自主行動時，安全設計的重心必須從機率性的模型層轉移到開發者完全可控的 **Application Layer**，透過微服務化邊界、零信任最小權限、確定性人工介入與獨立代理身分，將不可預測的模型推理轉化為可控制爆炸半徑的系統行為。

## 來源背景與過濾說明
- **性質**：Microsoft Security Blog 安全架構指南（2026-05-14）。
- **去雜訊處理**：已略過文末 Microsoft Security 產品導流與社群追蹤連結，保留四層防禦模型與四項應用層設計模式。

## 核心論點索引

### 1. 四層縱深防禦與應用層的決定性地位
- 區分 *Model Layer*、*Safety System Layer*、*Application Layer* 與 *Positioning Layer*，指出只有 *Application Layer* 能將機率性模型輸出轉化為確定性系統結果。
- 詳見：[縱深防禦（Defense in Depth）](../defenses/defense-in-depth.md)

### 2. 應用層設計模式：微服務化、最小權限與獨立身分
- 反對「萬能代理（Everything Agent）」的反模式，主張比照分散式系統切割職責、採 Zero-trust 任務級／時間級授權，並為每個 Agent 配賦可獨立追蹤與撤銷的 *Agent Identity*。
- 詳見：[最小權限與獨立代理身分](../defenses/least-privilege-and-agent-identity.md)

### 3. 確定性 Human-in-the-Loop（HITL）治理
- 強調不能讓機率性模型自行決定何時需要人類審核，必須由編排層（Orchestrator）在程式碼中強制執行確定性升級觸發條件。
- 詳見：[確定性人工介入與執行期控制](../defenses/deterministic-hitl-and-runtime-controls.md)

### 4. 綜合脈絡
- 詳見：[AI Agent 安全全景與跨來源對照](../synthesis/ai-agent-security-landscape.md)
