# AGILAB 軟體開發手冊

[![Documentation](https://img.shields.io/badge/docs-GitHub%20Pages-blue.svg)](https://agilab-ntnu.github.io/AGILAB_Software_Lab_Guide/)

歡迎加入 AGILAB！本手冊是你在實驗室開發研究專案的完整指南。**建議依照以下順序閱讀**，每章約 5–10 分鐘。

---

## 新生閱讀路線

!!! tip "第一次進實驗室？從這裡開始"
    依照下方步驟順序讀完，你就能獨立建立並管理一個符合實驗室規範的研究專案。

**專案啟動**

| 步驟 | 章節 | 你會學到什麼 | 所需時間 |
|:---:|---|---|:---:|
| 1 | [快速啟動](getting_started/contributing.md) | Clone 老師建立的專案、了解實驗室開發規範 | 15 分鐘 |
| 2 | [環境管理](getting_started/conda_guide.md) | 如何用 Conda 建立可重現的開發環境 | 10 分鐘 |
| 3 | [模板結構說明](getting_started/template_structure.md) | 每個資料夾與設定檔的用途 | 10 分鐘 |

**日常開發**

| 步驟 | 章節 | 你會學到什麼 | 所需時間 |
|:---:|---|---|:---:|
| 4 | [程式碼規範](development/coding_standards.md) | 風格規範、型別提示、Docstrings | 10 分鐘 |
| 5 | [日常 Git 操作](development/daily_git.md) | Fork 工作流、每日 commit 與 PR 流程 | 15 分鐘 |
| 6 | [Commit 規範](getting_started/git_convention.md) | 如何寫出讓隊友看懂的 Git commit 訊息 | 5 分鐘 |
| 7 | [Logging 與除錯](development/logging_debugging.md) | 用 logging 取代 print、讀懂錯誤訊息 | 10 分鐘 |
| 8 | [撰寫與執行測試](development/testing.md) | 用 pytest 保護你的程式邏輯 | 15 分鐘 |
| 9 | [實驗管理](development/experiment_management.md) | 讓每次實驗結果都可追溯、可重現 | 10 分鐘 |

**研究規範**

| 步驟 | 章節 | 你會學到什麼 | 所需時間 |
|:---:|---|---|:---:|
| 10 | [研究可重現性](research/reproducibility.md) | 種子設定、決定性演算法、資料版本化 | 10 分鐘 |
| 11 | [專案生命週期](research/lifecycle.md) | 從開案到論文發表後的程式碼管理 SOP | 10 分鐘 |


---

## 基礎建設

- **[SoftwareTemplate](https://github.com/AGILAB-NTNU/SoftwareTemplate)**: 所有研究專案的起點，內含標準目錄結構與自動化 Lint。

## 聯絡資訊

如果你對規範有任何建議，請聯繫閔傑老大或透過 Issue 提出討論。

---

**下一步 →** [快速啟動](getting_started/contributing.md)
