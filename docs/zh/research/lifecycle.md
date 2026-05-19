# 專案生命週期指南 (Project Lifecycle Guide)

本指南定義了在 AGILAB 進行研究專案從「啟動」到「結案封存」的標準化階段。

## 1. 啟動階段 (Initiation)

1.  **建立 Repo**：基於 [SoftwareTemplate](https://github.com/AGILAB-NTNU/SoftwareTemplate) 建立新倉庫。
2.  **定義數據存放**：確定大型資料集是存放在伺服器 `/data` 路徑還是外部儲存裝置。

## 2. 開發階段 (Development)

*   **小步提交**：養成頻繁 `git commit` 的習慣，且 Message 須具備描述性。
*   **同步指南**：每當 `environment.yml` 變更，務必通知團隊成員。
*   **定期測試**：確保 `pytest` 隨時都能跑通。

## 3. 論文發表前夕 (Pre-publication)

在提交論文或釋出 Preprint 前，請執行以下檢查：
1.  **程式碼清理**：移除所有 `print` 除錯語句，改用 `logging`。
2.  **文件補完**：確保 README 中有完整的安裝與執行範例。
3.  **設定 License**：預設推薦使用 **MIT License** 或 **Apache-2.0**。
4.  **鎖定版本**：為當前程式碼打上 Git Tag（例如 `v1.0-submission`）。

## 4. 結案與封存 (Archive)

論文發表後，為了學術誠信與交接，必須：
1.  **伺服器權重備份**：
    *   將最終模型權重 (Best Checkpoints) 移至實驗室統一存放路徑（例如：`/data/checkpoints/`）。
    *   **命名規範**：`[專案名]_[日期]_[關鍵指標].pt` (例如：`Humanoid_20240501_reward300.pt`)。
2.  **路徑紀錄**：在專案 README 的「結案」區塊中，必須明確標示該權重所在的**伺服器代號 (Hostname)** 與**絕對路徑**（例如：`Server: G108-A6000, Path: /data/checkpoints/...`）。
3.  **交接文檔**：若有後續接手的學弟妹，請撰寫 `INTERNAL_HANDOVER.md`，說明實驗環境中特殊的機器人硬體接口或資料路徑。

---

!!! danger "嚴禁將未清理的程式碼直接公開"
    程式碼中若包含硬編碼路徑或個人帳密，必須先清理後才能公開。

---

!!! success "你已完成所有章節！"
    恭喜讀完 AGILAB 軟體開發手冊的全部內容。遇到不懂的術語隨時可以查閱[附錄頁面](../index.md)。

**← 回到首頁** [AGILAB 軟體開發手冊](../index.md)
