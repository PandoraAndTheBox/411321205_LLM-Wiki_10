---
title: What Is the AI Agent Attack Surface?
type: source
tags: [attack-surface, mcp, multi-agent, threat-landscape]
updated: 2026-11-24
source_file: raw/Title What Is the AI Agent Attack S.txt
source_url: https://www.paloaltonetworks.com/cyberpedia/ai-agent-attack-surface
published: unknown
---

# What Is the AI Agent Attack Surface?

> **一句話核心主張**：AI Agent 的攻擊面遠超傳統 LLM 應用的提示與回應，涵蓋指令規劃、工具與 MCP 連線、身分憑證、外部資料源、持久性記憶、代理間通訊與編排基礎設施八大層面，安全控制必須貫穿整個工作流程而非僅守住出入口。

## 來源背景與過濾說明
- **性質**：Palo Alto Networks Cyberpedia 概念總覽文章。
- **去雜訊處理**：原始檔案前段（第 1–595 行）與末段（第 811–1024 行）包含大量網站導覽選單、Cookie 橫幅與商業產品廣告（如 Prisma AIRS），已全數過濾，僅萃取第 596–810 行正文之中立架構知識。

## 核心論點索引

### 1. AI Agent 攻擊面模型與傳統 LLM 應用之差異
- 定義八大攻擊面組成要素，並從功能、外部存取、身分、狀態、營運影響與通訊六個維度對比 *LLM Application* 與 *AI Agent*。
- 詳見：[AI Agent 攻擊面（Agentic Attack Surface）](../concepts/agentic-attack-surface.md)

### 2. 跨攻擊面常見威脅與緩解對應
- 整理八類主要威脅（含 *Agent Impersonation*、*Excessive Agency*、*Credential Compromise* 等）與縮減攻擊面的八項治理原則，特別點出 **Model Context Protocol (MCP)** 連線的治理盲點。
- 詳見：[Prompt Injection](../threats/prompt-injection.md)、[Tool Misuse & Privilege Escalation](../threats/tool-misuse-and-privilege-escalation.md)、[最小權限與獨立代理身分](../defenses/least-privilege-and-agent-identity.md)

### 3. 攻防雙面視角
- 簡述攻擊者亦開始利用自主 AI Agent 自動化執行偵察與漏洞利用（引述 Unit 42 研究），強調防禦端需同時保護自家 Agent 並防範自動化攻擊。
- 詳見：[AI Agent 安全全景與跨來源對照](../synthesis/ai-agent-security-landscape.md)
