# 貢獻指南 (Contributing Guidelines)

歡迎來到 AGILAB！為了維持高品質的研究標準並確保研究的可重現性，在進行專案開發時，請務必遵守以下規範。

## 快速啟動：如何建立新專案

當您要開始一個新的研究專案時，請先參考 [AGILAB Software Template](https://github.com/AGILAB-NTNU/SoftwareTemplate)。

建立新專案的步驟如下：

1. **複製並初始化**
   1.  在 GitHub 上點擊 `Use this template` 建立新的 Repository。
   2.  將專案 Clone 到本地環境。
   3.  **重命名核心資料夾**：將 `src/project_name/` 改為您的專案名稱（例如 `src/my_robot_rl/`）。
   4.  **修改專案資訊**：打開 `pyproject.toml`，修改 `name = "project_name"` 為您的專案名稱。

2. **環境建置**
   我們統一使用 Conda 來管理複雜的物理模擬與 CUDA 依賴：
   ```bash
   # 根據 environment.yml 建立環境
   conda env create -f environment.yml
   conda activate agilab_env
   ```

3. **驗證安裝（重要）**
   確保您的套件已被正確安裝為開發模式 (Editable mode)：
   ```bash
   # 在終端機測試是否能 import
   python -c "import 您的專案名稱; print('安裝成功！')"
   ```

## 實驗室工作流 (Lab Workflow)

### 1. 分支策略 (Branching)
- `main`: 穩定分支，僅存放已發表論文或正式釋出的程式碼。
- `dev`: 整合分支，用於日常開發與實驗。
- `feature/[名稱]`: 個人功能或特定實驗分支。

### 2. 開發流程
1. **同步環境**: 始終使用 `environment.yml` 以確保所有人的依賴環境一致。
2. **實作程式碼**: 在 `src/project_name/` 中撰寫您的核心邏輯。
3. **排版與檢查**: 我們使用 **Ruff**。提交前請確保通過 `ruff check .` 與 `ruff format .`。
4. **撰寫測試**: 針對核心邏輯，請在 `tests/` 中新增或更新單元測試。

### 3. 拉取請求 (Pull Requests)
- 所有 PR 應從個人分支合併至 `dev` 分支。
- 確保 GitHub Actions (CI) 測試全數通過。
- 在 PR 描述中清晰說明該次實驗的動機、方法與預期結果。

### 4. Git Commit 規範
為了確保研究紀錄的可追蹤性，請遵守實驗室的 [Git Commit 訊息規範](git_convention.md)。
- **格式**：`Type: English Description`
- **範例**：`feat: Add new perception layer`

## 程式碼規範 (Coding Standards)

- **風格**: 嚴格遵守 Google Python Style Guide。
- **型別提示**: 所有函式簽名必須包含 **Type Hinting**。
- **註解**: 所有公開模組、類別與函式皆須使用 Google Style Docstrings。
- **可重現性**: 務必使用隨機種子鎖定工具，確保隨機性過程可追蹤。

## 文件維護

- 若新增相依套件，請務必更新 `environment.yml` 與 `pyproject.toml`。
- 若實作了新的實驗流程，請在 `README.md` 中新增使用範例。
