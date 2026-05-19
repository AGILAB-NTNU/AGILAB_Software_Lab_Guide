# 快速啟動

歡迎來到 AGILAB！本頁說明你加入新專案後的一次性初始化流程。

---

## 專案是怎麼來的？

每個研究專案的 Repository 由**指導老師**統一從 [SoftwareTemplate](https://github.com/AGILAB-NTNU/SoftwareTemplate) 建立在實驗室的 GitHub 組織下（`AGILAB-NTNU`）。學生不需要自己建立 repo，**加入後的第一步是 Fork**。

---

## 初始化流程（只做一次）

### 步驟 1：Fork 實驗室 repo

前往實驗室 repo 頁面，點右上角的 **Fork** 按鈕。
Fork 完成後，你的帳號下會出現一個同名的 repo（`YOUR_NAME/project`），這是你的個人工作空間。

### 步驟 2：Clone 你的 fork

```bash
git clone https://github.com/YOUR_NAME/project.git
cd project
```

### 步驟 3：將實驗室 repo 設為 upstream

```bash
git remote add upstream https://github.com/AGILAB-NTNU/project.git
```

你 clone 的是自己的 fork，`origin` 指向你自己的 repo。
`upstream` 是實驗室的 repo，是所有人共同的進度來源。
當隊友的 PR 被合入實驗室 `dev` 後，你需要從 `upstream` 把這些更新同步回來：

```bash
git fetch upstream
git merge upstream/dev
```

### 步驟 4：重命名核心資料夾

```bash
mv src/project_name src/my_robot_rl  # 換成實際專案名稱
```

並更新 `pyproject.toml`：

```toml
[project]
name = "my_robot_rl"   # ← 改成實際專案名稱
```

### 步驟 5：建立 Conda 環境並驗證

```bash
conda env create -f environment.yml
conda activate agilab_env
python -c "import my_robot_rl; print('安裝成功！')"
```

!!! tip "想知道每個資料夾是做什麼的？"
    → [專案模板結構說明](template_structure.md)

---

**下一步 →** [環境管理](conda_guide.md)
