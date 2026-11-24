# Wiki Index

本索引為知識庫的內容總覽，按分類記錄所有頁面與一句話摘要。Agent 在進行 Query 與 Ingest 前應先查閱此檔。

---

## Sources（單篇來源導讀）

- [AI Memory Security: Best Practices and Implementation](wiki/sources/mem0-ai-memory-security.md) — 整理針對 *Persistent Memory* 的三大記憶中毒研究（MINJA、AgentPoison、MemoryGraft）與六階段分層防禦架構。（對應 `raw/Title AI Memory Security Best Pract.txt`）
- [Defense in depth for autonomous AI agents](wiki/sources/microsoft-defense-in-depth-agents.md) — Microsoft 提出的四層縱深防禦模型，強調以 *Application Layer* 四項設計模式控制自主代理的爆炸半徑。（對應 `raw/Title Defense in depth for autonomo.txt`）
- [LLM01:2025 Prompt Injection](wiki/sources/owasp-llm01-2025-prompt-injection.md) — OWASP GenAI 2025 標準風險條目，定義 Direct / Indirect / Multimodal *Prompt Injection* 與七項緩解原則。（對應 `raw/Title LLM012025 Prompt Injection.txt`）
- [Tool Misuse & Privilege Escalation](wiki/sources/topaithreats-tool-misuse-privilege-escalation.md) — Top AI Threats（`PAT-AGT-006`）分析工具濫用與權限提升的五大成因、真實／評估越權案例與偵測應變框架。（對應 `raw/Title Tool Misuse & Privilege Escal.txt`）
- [What Is the AI Agent Attack Surface?](wiki/sources/paloalto-ai-agent-attack-surface.md) — 系統性定義 AI Agent 八大攻擊面、與傳統 LLM 應用之六維度差異，以及含 MCP 治理在內的縮減策略。（對應 `raw/Title What Is the AI Agent Attack S.txt`）

## Threats（威脅與攻擊向量）

- [Prompt Injection](wiki/threats/prompt-injection.md) — 涵蓋 Direct / Indirect / Multimodal 注入、混淆繞過手法與在 Agentic 系統中的連鎖危害。
- [Memory Poisoning](wiki/threats/memory-poisoning.md) — 跨會話長期記憶中毒（OWASP ASI06），彙整 MINJA、AgentPoison、MemoryGraft 與 A-MemGuard 實測數據。
- [Tool Misuse & Privilege Escalation](wiki/threats/tool-misuse-and-privilege-escalation.md) — 代理超越工具授權邊界或串接工具鏈越權（含無外部攻擊者時的目標導向自主越權）。

## Defenses（防禦架構與原則）

- [縱深防禦（Defense in Depth）](wiki/defenses/defense-in-depth.md) — 整合 Microsoft 四層緩解模型與 Mem0 記憶生命週期六階段防禦，說明應用層的決定性角色。
- [最小權限與獨立代理身分（Least Privilege & Agent Identity）](wiki/defenses/least-privilege-and-agent-identity.md) — 微服務化代理切割、零信任任務級授權、獨立代理身分與 MCP 連線治理原則。
- [確定性人工介入與執行期控制（Deterministic HITL & Runtime Controls）](wiki/defenses/deterministic-hitl-and-runtime-controls.md) — 由編排層強制執行的 HITL 閘門、輸入輸出與 RAG Triad 驗證、複合信任評分與 TTL 回滾機制。

## Concepts（基礎概念與系統邊界）

- [AI Agent 攻擊面（Agentic Attack Surface）](wiki/concepts/agentic-attack-surface.md) — 解析 AI Agent 八大攻擊面組成，並對比傳統 LLM 應用與自主代理在行動、狀態與身分上的風險差異。
- [信任邊界與持久性記憶（Trust Boundary & Persistent Memory）](wiki/concepts/trust-boundary-and-persistent-memory.md) — 探討語意通道混合造成的信任邊界崩解，以及 RAG 與記憶層在 *Semantic Imitation Heuristic* 下的信任落差。

## Synthesis（跨來源比較與讀書會討論）

- [AI Agent 安全全景與跨來源對照](wiki/synthesis/ai-agent-security-landscape.md) — 整合五篇來源的對映矩陣、跨文獻互補洞見與讀書會延伸討論問題。
