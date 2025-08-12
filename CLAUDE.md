# Claude Code 開發代理團隊使用指南

使用方式：將以下內容寫到專案的claude.md頂端

## Agent 團隊開發框架

### 新功能開發
當接收到開發或編寫新功能相關的指令後，請依照 `Development_Team_Framework.md` 裡面的規範透過 agent 團隊來完成任務。

**核心工具要求**：
1. **PRD文檔**：必須使用 `templates/PRD_Template.md` 模板
2. **流程圖**：必須使用 Mermaid 繪製業務流程、用戶旅程、時序圖
3. **C4架構圖**：必須包含完整4層架構圖（系統上下文、容器、組件、代碼）
4. **圖表規範**：參考 `templates/Diagrams_Guide.md`

### Bug 修復和問題解決
當接收到修改或解決 bug 問題相關的指令後，請依照 `Fix_Team_Framework.md` 裡面的規範透過 agent 團隊來完成任務。

## 🎯 專案簡介

本專案包含一套完整的 AI 開發代理團隊框架，包括開發團隊和修復團隊，每個代理都有明確的職責和專業規範。

## 📋 使用說明

### 環境要求
- 需要在專案目錄下的虛擬環境中執行相關命令
- 確保所有文檔都保持最新狀態

### 團隊選擇
1. **開發新功能**：參考 `Development_Team_Framework.md`
2. **修復問題**：參考 `Fix_Team_Framework.md`

### 代理使用
- 每個代理的詳細規範請參考 `agents/` 目錄下的對應文檔
- 嚴格按照代理規範執行工作流程

## 🔄 更新流程

在每次 git push 前：
1. 檢查是否需要更新 README.md
2. 檢查是否需要更新 CLAUDE.md
3. 確保所有文檔內容保持同步

## 🚀 開發命令

```bash
# 初始化專案
git init
git remote add origin <repository_url>

# 提交變更
git add .
git commit -m "commit message"
git push -u origin main
```

## 📝 注意事項

- 所有操作都在專案虛擬環境下進行
- 遵循各代理的專業規範
- 保持文檔的一致性和完整性