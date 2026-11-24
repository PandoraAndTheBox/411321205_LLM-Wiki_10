---
title: Prompt Injection
type: threat
tags: [prompt-injection, owasp-llm01, jailbreaking, indirect-injection]
updated: 2026-11-24
---

# Prompt Injection

## 定義與本質成因
*Prompt Injection* 是指透過輸入內容操縱 LLM 的推理與行為，使其偏離原本的系統指令或安全規範。這類輸入不一定需要對人類可見（例如隱藏於網頁或圖片中），只要模型解析了該內容就可能觸發。

- **本質限制**：生成式模型將「系統指令」與「外部資料」置於同一語意上下文中處理（缺乏硬體或程式層級的指令／資料隔離）。[OWASP LLM01:2025](../sources/owasp-llm01-2025-prompt-injection.md) 明確指出，即使採用 *RAG* 或微調（Fine-tuning），仍無法徹底消除 *Prompt Injection* 漏洞。
- **與 Jailbreaking 的差異**：兩者常被混用，但 *Prompt Injection* 泛指透過特定輸入改變模型行為（包含但不限於繞過安全限制）；*Jailbreaking* 則是 *Prompt Injection* 的一種特定形式，目的是讓模型完全無視安全防護協定。

## 主要攻擊分類與變體

根據 [OWASP LLM01:2025](../sources/owasp-llm01-2025-prompt-injection.md) 與 [Palo Alto Networks](../sources/paloalto-ai-agent-attack-surface.md) 的整理，主要變體包含：

1. **Direct Prompt Injection（直接注入）**：使用者輸入直接覆寫或干擾模型指令（MITRE ATLAS `AML.T0051.000`）。可能是惡意攻擊者刻意構造，也可能是無意觸發（*Unintentional Injection*）。
2. **Indirect Prompt Injection（間接注入）**：惡意指令隱藏於外部網頁、電子郵件、文件或知識庫（MITRE ATLAS `AML.T0051.001`）。當 Agent 擷取或摘要這些外部資源時，將「資料」誤認為「指令」執行，是 Agentic 系統最具破壞力的入口。
3. **Multimodal Injection（多模態注入）**：將惡意提示嵌入圖片等非文字模態中，搭配無害文字繞過純文字過濾器。
4. **混淆與繞過手法**：
   - *Payload Splitting*：將惡意提示拆散在不同片段（例如履歷不同區塊），由模型組合後才觸發。
   - *Adversarial Suffix*：在提示後方附加看似無意義的字元序列以繞過安全對齊。
   - *Multilingual / Obfuscated Attack*：使用多語言混寫、Base64 或 Emoji 編碼逃避關鍵字過濾。

## 在 Agentic 系統中的連鎖影響
在純對話應用中，*Prompt Injection* 通常只影響單次輸出內容；但在具備工具呼叫與記憶的 AI Agent 中，影響會被顯著放大（參見 [AI Agent 攻擊面](../concepts/agentic-attack-surface.md)）：
- 觸發未授權的工具呼叫與系統指令執行，進而演變為 [Tool Misuse & Privilege Escalation](./tool-misuse-and-privilege-escalation.md)。
- 透過隱藏圖片 URL 等方式外洩機密對話或企業資料。
- 若惡意指令被寫入長期記憶，會轉化為跨會話的 [Memory Poisoning](./memory-poisoning.md)。

## 代表性案例（間接引用）
- **CVE-2024-5184（Code Injection in Email Assistant）**：OWASP 記錄的真實漏洞情境，攻擊者利用 LLM 郵件助理的 *Prompt Injection* 存取敏感資訊並竄改郵件內容。
- **Indirect Prompt Injection via Webpage / PDF**：OWASP 引用 Greshake et al. ([arXiv:2302.12173](https://arxiv.org/pdf/2302.12173.pdf)) 與 *Inject My PDF* 等研究，展示如何透過外部網頁或履歷 PDF 劫持整合 LLM 的應用程式。

## 相關防禦原則
- [縱深防禦（Defense in Depth）](../defenses/defense-in-depth.md)（透過應用層承擔確定性防線，不依賴模型自身免疫力）
- [最小權限與獨立代理身分](../defenses/least-privilege-and-agent-identity.md)（限制模型被劫持後的爆炸半徑）
- [確定性人工介入與執行期控制](../defenses/deterministic-hitl-and-runtime-controls.md)（不可信內容標記、RAG Triad 驗證、高風險操作 HITL）
