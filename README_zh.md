# AGILAB 軟體開發手冊

歡迎來到 AGILAB 軟體開發與研究規範手冊。本倉庫匯集了實驗室所有專案通用的開發標準、環境配置與協作流程。

[English Version](README.md)

## 📖 核心規範

*   **[快速啟動與貢獻指南 (SOP)](docs/zh/contributing.md)**: 
    *   如何使用 [SoftwareTemplate](https://github.com/AGILAB-NTNU/SoftwareTemplate) 建立新專案。
    *   分支策略 (Main/Dev/Feature) 與開發流程。
*   **[研究可重現性指南](docs/zh/reproducibility.md)**:
    *   隨機種子鎖定 (Seed Everything) 的標準實作。
    *   決定性演算法 (Determinism) 與環境一致性規範。
*   **[環境管理指南 (Conda & Environment)](docs/zh/conda_guide.md)**:
    *   處理 CUDA、物理模擬器與複雜依賴的實踐。
*   **[專案生命週期 (Project Lifecycle)](docs/zh/lifecycle.md)**:
    *   從開案開發到論文發表後的程式碼封存與開源 SOP。

## 🛠️ 基礎建設

*   **[軟體範本 (SoftwareTemplate)](https://github.com/AGILAB-NTNU/SoftwareTemplate)**: 
    所有研究專案的起點，內含標準目錄結構、Ruff 程式碼檢查與自動化文件站。

## 🤝 聯絡資訊

如果您對規範有任何建議，請聯繫閔傑老大或透過 Issue 提出討論。