---
title: LLM01:2025 Prompt Injection
type: source
tags: [owasp, prompt-injection, standard, jailbreaking]
updated: 2026-11-24
source_file: raw/Title LLM012025 Prompt Injection.txt
source_url: https://genai.owasp.org/llmrisk/llm01-prompt-injection/
published: 2025
---

# LLM01:2025 Prompt Injection

> **一句話核心主張**：*Prompt Injection* 源於生成式模型將指令與資料混在同一語意通道中處理的本質限制，且 RAG 與微調皆無法徹底根治，因此必須依賴外部輸出驗證、最小權限隔離與不可信內容區隔來控制影響範圍。

## 來源背景與過濾說明
- **性質**：OWASP GenAI Security Project 2025 年度標準風險條目（`LLM01:2025`）。
- **註記**：原文 `Published Time` 欄位標示為抓取時間（2026-11-24），條目標題年份為 2025。

## 核心論點索引

### 1. 威脅定義、類型與攻擊情境
- 區分 *Direct* 與 *Indirect Prompt Injection*、釐清與 *Jailbreaking* 的差異，並涵蓋 *Multimodal Injection*、*Payload Splitting*、*Adversarial Suffix* 與多語言／編碼混淆等九種情境。
- 詳見：[Prompt Injection](../threats/prompt-injection.md)

### 2. 七項預防與緩解原則
- 包含限制模型角色與行為、強制確定性輸出格式驗證、RAG Triad 語意過濾、應用程式端最小權限與 Token 隔離、高風險操作 HITL 審批、外部不可信內容標記與紅隊對抗測試。
- 詳見：[確定性人工介入與執行期控制](../defenses/deterministic-hitl-and-runtime-controls.md)、[最小權限與獨立代理身分](../defenses/least-privilege-and-agent-identity.md)

### 3. 綜合脈絡
- 詳見：[AI Agent 安全全景與跨來源對照](../synthesis/ai-agent-security-landscape.md)
