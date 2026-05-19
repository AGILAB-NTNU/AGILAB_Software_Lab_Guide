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
| 1 | [環境管理](conda_guide.md) | 如何用 Conda 建立可重現的開發環境 | 10 分鐘 |
| 2 | [快速啟動](contributing.md) | Clone 老師建立的專案、撰寫符合規範的程式碼 | 15 分鐘 |
| 3 | [模板結構說明](template_structure.md) | 每個資料夾與設定檔的用途 | 10 分鐘 |
| 4 | [Commit 規範](git_convention.md) | 如何寫出讓隊友看懂的 Git commit 訊息 | 5 分鐘 |

**日常開發**

| 步驟 | 章節 | 你會學到什麼 | 所需時間 |
|:---:|---|---|:---:|
| 5 | [日常 Git 操作](daily_git.md) | 分支管理、PR 流程、處理 Conflict | 15 分鐘 |
| 6 | [撰寫與執行測試](testing.md) | 用 pytest 保護你的程式邏輯 | 15 分鐘 |
| 7 | [Logging 與除錯](logging_debugging.md) | 用 logging 取代 print、讀懂錯誤訊息 | 10 分鐘 |
| 8 | [實驗管理](experiment_management.md) | 讓每次實驗結果都可追溯、可重現 | 10 分鐘 |

**研究規範**

| 步驟 | 章節 | 你會學到什麼 | 所需時間 |
|:---:|---|---|:---:|
| 9 | [研究可重現性](reproducibility.md) | 種子設定、決定性演算法、資料版本化 | 10 分鐘 |
| 10 | [專案生命週期](lifecycle.md) | 從開案到論文發表後的程式碼管理 SOP | 10 分鐘 |

---

## 附錄：術語說明

初次遇到不懂的術語時，可以直接查閱：

- [Google Python Style Guide](google_style_guide.md) — 命名、行長、Import 順序等實用規則整理
- [Type Hinting 入門](type_hinting.md) — 函式型別標注是什麼、為何重要
- [開發模式安裝（Editable Install）](editable_install.md) — `pip install -e .` 到底做了什麼
- [Google Style Docstrings](docstrings.md) — 如何為函式寫出規範的說明文件

---

## 基礎建設

- **[SoftwareTemplate](https://github.com/AGILAB-NTNU/SoftwareTemplate)**: 所有研究專案的起點，內含標準目錄結構與自動化 Lint。

## 聯絡資訊

如果你對規範有任何建議，請聯繫閔傑老大或透過 Issue 提出討論。

---

**下一步 →** [環境管理](conda_guide.md)
