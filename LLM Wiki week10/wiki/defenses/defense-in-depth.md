---
title: 縱深防禦（Defense in Depth）
type: defense
tags: [defense-in-depth, architecture, application-layer, memory-lifecycle]
updated: 2026-11-24
---

# 縱深防禦（Defense in Depth）

## 核心理念
當 AI Agent 具備自主規劃、工具呼叫與持久記憶能力後，錯誤傳播更快、爆炸半徑（Blast Radius）更大，且復原成本大幅提高。單一防線（例如僅靠模型安全微調或單一輸入過濾器）皆不足以阻擋攻擊，必須在系統多個層級建立互補且獨立的防護控制。

## Microsoft 四層緩解架構

根據 [Microsoft Security Blog (2026)](../sources/microsoft-defense-in-depth-agents.md)，Agentic AI 的安全架構可拆分為四個相互強化的層級：

| 層級 | 核心職責 | 性質與限制 |
| :--- | :--- | :--- |
| **Model Layer（模型層）** | 透過訓練資料、微調與拒絕行為塑造模型推理傾向 | **機率性（Probabilistic）**，可降低風險但無法提供安全保證。 |
| **Safety System Layer（安全系統層）** | 提供執行期內容過濾、Guardrails、Groundedness 偵測與可觀測性 | 負責偵測異常與過濾有害輸出，但無法彌補權限過大的架構缺陷。 |
| **Application Layer（應用層）** | 定義 Agent 的架構邊界、工具權限、工作流程與確定性升級路徑 | **決定性控制層（Decisive Layer）**：開發者唯一完全可控的層級，負責將機率性的模型行為轉化為確定性的系統結果。 |
| **Positioning Layer（定位層）** | 透過透明度文件與 UX 揭露系統能力與限制 | 塑造使用者預期，避免不當依賴（Inappropriate Reliance）。 |

> **關鍵洞見**：沒有任何上層（Model / Safety）能替代架構良好的 Application Layer。即使模型經過良好微調，若缺乏應用層的確定性邊界，仍可能因對抗輸入或目標漂移而造成越權。

## 記憶層的六階段縱深防禦

針對長期記憶這個跨會話攻擊面，[Mem0 安全指南](../sources/mem0-ai-memory-security.md) 將縱深防禦具體化為貫穿記憶生命週期的六個階段：
1. **輸入淨化（Input Sanitization）**：寫入記憶前掃描長度、敏感資料（PII、憑證）與已知注入模式。
2. **隔離儲存（Scoped Storage）**：以獨立命名空間隔離不同使用者／會話，對條目進行雜湊完整性驗證並設定 TTL。
3. **存取控制（Access Control）**：對記憶的讀寫刪操作實施 RBAC 與綁定身分的驗證權杖。
4. **信任評分檢索（Trust-Scored Retrieval）**：檢索時綜合時間新鮮度、來源可靠度與模式一致性進行排序與過濾。
5. **輸出驗證（Output Validation）**：生成回應後檢查是否外洩機密或出現異常行為模式。
6. **持續監控與回滾（Continuous Monitoring）**：完整記錄記憶操作日誌，偵測寫入頻率或檢索分布異常，並保留鑑識快照以供回滾。

## 關聯原則
- [最小權限與獨立代理身分](./least-privilege-and-agent-identity.md)（Application Layer 的核心結構控制）
- [確定性人工介入與執行期控制](./deterministic-hitl-and-runtime-controls.md)（Application Layer 與 Safety Layer 的執行期閘門）
- [AI Agent 攻擊面](../concepts/agentic-attack-surface.md)
