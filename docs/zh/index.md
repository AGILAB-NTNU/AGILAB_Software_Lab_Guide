# AGILAB 軟體開發手冊

[![Documentation](https://img.shields.io/badge/docs-GitHub%20Pages-blue.svg)](https://agilab-ntnu.github.io/AGILAB_Software_Lab_Guide/)

歡迎加入 AGILAB！本手冊是你在實驗室開發研究專案的完整指南。**建議依照以下順序閱讀**，每章約 5–10 分鐘。

---

## 新生閱讀路線

!!! tip "第一次進實驗室？從這裡開始"
    依照下方步驟順序讀完，你就能獨立建立並管理一個符合實驗室規範的研究專案。

| 步驟 | 章節 | 你會學到什麼 | 所需時間 |
|:---:|---|---|:---:|
| 1 | [環境管理](conda_guide.md) | 如何用 Conda 建立可重現的開發環境 | 10 分鐘 |
| 2 | [快速啟動](contributing.md) | 從模板建立新專案、撰寫符合規範的程式碼 | 15 分鐘 |
| 3 | [Commit 規範](git_convention.md) | 如何寫出讓隊友看懂的 Git commit 訊息 | 5 分鐘 |
| 4 | [研究可重現性](reproducibility.md) | 確保你的實驗結果可以被他人重現 | 10 分鐘 |
| 5 | [專案生命週期](lifecycle.md) | 從開案到論文發表後的程式碼管理 SOP | 10 分鐘 |

---

## 附錄：術語說明

初次遇到不懂的術語時，可以直接查閱：

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
