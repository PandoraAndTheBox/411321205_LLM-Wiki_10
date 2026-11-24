---
title: 最小權限與獨立代理身分（Least Privilege & Agent Identity）
type: defense
tags: [least-privilege, agent-identity, microservices, zero-trust, mcp]
updated: 2026-11-24
---

# 最小權限與獨立代理身分（Least Privilege & Agent Identity）

## 解決的核心威脅
- 緩解 [Tool Misuse & Privilege Escalation](../threats/tool-misuse-and-privilege-escalation.md) 中的預設權限過寬與工具鏈越權。
- 限縮 [Prompt Injection](../threats/prompt-injection.md) 成功劫持模型後的爆炸半徑（Blast Radius）。
- 防止跨使用者與跨代理的 [Memory Poisoning](../threats/memory-poisoning.md) 橫向污染。

## 三大核心架構原則

### 1. 將 Agent 設計為微服務（Agents as Microservices）
- **反模式（Anti-pattern）**：[Microsoft 安全指南](../sources/microsoft-defense-in-depth-agents.md) 警告應避免「萬能代理（Everything Agent）」——即單一 Agent 擁有過多工具、過寬權限與模糊職責。每多接一個工具就擴大一分攻擊面，且模糊指令會提高任務漂移風險。
- **架構解法**：比照分散式系統原則，將 Agent 切割為職責明確、邊界清晰的小型元件，透過編排（Orchestration）組合複雜能力，而非賦予單一 Agent 廣泛授權。

### 2. 零信任與細粒度最小權限（Zero-Trust Least Permissions）
- **預設零存取**：所有工具呼叫、資料存取與外部整合都必須經過明確授權，而非預設開放（[Microsoft](../sources/microsoft-defense-in-depth-agents.md)、[TopAIThreats](../sources/topaithreats-tool-misuse-privilege-escalation.md)、[OWASP LLM01](../sources/owasp-llm01-2025-prompt-injection.md)）。
- **任務級／時間級授權**：權限應盡可能綁定單一任務的生命週期（任務完成即失效），其次才是設定時效性憑證（Short-lived credentials），並嚴格區分唯讀與寫入權限。
- **程式端掌控憑證**：OWASP 建議由應用程式本身管理 API Token 並於程式碼中執行延伸功能，而非將高權限 Token 或邏輯直接暴露給模型。

### 3. 獨立且可驗證的代理身分（Agent Identity as a Security Primitive）
- **為何不能共用人類身分**：人類使用者往往擁有過多權限；若 Agent 直接繼承使用者全部權限或共用服務帳號，便無法實施最小權限，也無法分辨操作究竟是「使用者本人」、「Agent 自主執行」還是「Agent 代表使用者執行」。
- **獨立身分的三大價值**（[Microsoft](../sources/microsoft-defense-in-depth-agents.md)、[Palo Alto Networks](../sources/paloalto-ai-agent-attack-surface.md)）：
  1. 精準授權：針對單一 Agent 綁定專屬權限與記憶命名空間（如 `user_id` + `agent_id`）。
  2. 生命週期治理：當單一 Agent 異常或被攻陷時，可單獨撤銷其憑證而不影響其他系統或使用者。
  3. 可歸責與可稽核：在多代理系統（Multi-agent）中，可追蹤代理間通訊與實際執行者，防範 *Agent Impersonation*。

## MCP（Model Context Protocol）與外部整合治理
[Palo Alto Networks](../sources/paloalto-ai-agent-attack-surface.md) 特別指出，MCP 雖標準化了工具與資料連線，但其 HTTP 傳輸授權為選配且無法取代企業存取治理。因此必須：
- 盤點所有核准與未核准的 MCP Server，封鎖或隔離未經授權的連線。
- 驗證每個整合的權限範圍，並持續審查外部工具與能力描述的變更（防範工具定義遭竄改或惡意 Server）。

## 權衡與限制
- 過度細碎的微服務切割會增加編排層（Orchestrator）的複雜度與延遲。
- 若多代理之間缺乏相互認證與訊息完整性驗證，僅切割單一 Agent 仍可能透過代理間通訊（Inter-agent communication）產生隱性信任傳遞風險。
