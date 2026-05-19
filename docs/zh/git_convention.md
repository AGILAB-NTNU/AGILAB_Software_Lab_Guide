# Git Commit 訊息規範 (Git Commit Convention)

在 AGILAB，我們追求研究的透明度與可追蹤性。雖然我們不要求複雜的工程流程，但為了讓團隊成員（以及未來的你）能快速理解程式碼的演進，請遵守以下最簡化的 Commit 訊息規範。

## 1. 核心公式
每個 Commit 訊息應遵循以下格式：

```text
Type: English Description
```

*   **Type**: 變更的類型。
*   **Description**: 簡短的英文敘述（動詞開頭）。

---

## 2. 常用類型 (Types)

我們建議優先使用以下 4 個常用類型，但您也可以視情況擴充：

| 類型 | 說明 | 範例 |
| :--- | :--- | :--- |
| **feat** | 新增功能、實驗模組或新的演算法實作 | `feat: Add attention-based policy network` |
| **fix** | 修復 Bug、修正參數錯誤或 Tensor Shape 不匹配 | `fix: Resolve gradient explosion in training` |
| **docs** | 更新文件、README 或程式碼註解 | `docs: Update installation steps for CUDA 12` |
| **refactor** | 重構或整理程式碼（不影響功能） | `refactor: Modularize reward functions` |

### 其他選用類型 (Optional Types)
若以上四種不敷使用，您也可以視變更性質使用更多類型，例如：

*   **exp**: 專用於實驗超參數的細微調整。
*   **perf**: 優化效能（如減少記憶體占用或加速運算）。
*   **chore**: 瑣碎事務（如更新套件版本、CI/CD 配置）。
*   **test**: 新增或修改測試程式碼。

---

## 3. 三大守則

1.  **使用英文 (English Only)**：為了與國際研究接軌，所有 Commit 訊息一律使用英文。
2.  **動詞開頭 (Imperative Mood)**：標題請使用動詞原型開頭（例如 `Add`, `Fix`, `Update`, `Remove`）。
3.  **保持簡短**：標題請控制在 50 個字元以內，不需要句點。

---

## 4. 快速範例 (Cheat Sheet)

*   **新增實驗模型**：`feat: Add LSTM-based episodic memory`
*   **修正物理參數**：`fix: Adjust friction coefficients for simulation`
*   **更新 Lab Guide**：`docs: Add Git commit convention guide`
*   **清理冗餘程式碼**：`refactor: Remove unused visualization scripts`

---

!!! tip
    良好的 Commit 紀錄是可重複研究的基石。當你需要回溯某次實驗結果時，清楚的紀錄會節省你大量的時間。

---

**下一步 →** [日常 Git 操作](daily_git.md)
