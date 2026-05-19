# 日常 Git 操作

本頁說明 AGILAB 實驗室的 Git 分工模式，以及每天開發時會用到的基本指令。

---

## 分支架構

```
main        ← 專案完成後才合併（由老師決定）
  └── dev   ← 整合分支，由主要負責人合併各人分支
        ├── james    ← 每位學生有自己的個人分支
        ├── alice
        └── bob
```

| 分支 | 誰管理 | 什麼時候合併 |
|---|---|---|
| `main` | 老師 | 計劃或專案正式結束後 |
| `dev` | 主要負責的學生 | 定期將各人分支合入 |
| 個人分支 | 自己 | 每天開發、自由 commit |

!!! info "你只需要管好自己的分支"
    一般學生只需要在自己的個人分支上工作並 push。`dev` 的合併由主要負責人處理，不需要自己操作。

---

## 每日工作流程

```
同步自己的分支 → 寫程式 → Commit → Push
```

---

## 1. 第一次：建立個人分支

加入專案後，**只需要做一次**：

```bash
# 從 dev 建立自己的分支（用你的名字或英文縮寫）
git checkout dev
git pull origin dev
git checkout -b james
git push -u origin james
```

之後每天的工作都在這個分支上進行。

---

## 2. 開始工作前：同步

每天開始前，先確認自己的分支是最新的：

```bash
git pull
```

---

## 3. 查看目前狀態

隨時用 `git status` 確認哪些檔案被修改了：

```bash
git status
```

輸出範例：

```
On branch james
Changes not staged for commit:
  modified:   src/your_project/models/policy.py

Untracked files:
  src/your_project/models/attention.py
```

- **modified**：已存在的檔案被修改
- **Untracked**：新建的檔案，Git 還沒追蹤

---

## 4. Commit 程式碼

**只 stage 你修改過的相關檔案**，不要一次 `git add .` 全加：

```bash
# Stage 特定檔案
git add src/your_project/models/policy.py
git add src/your_project/models/attention.py

# 確認 staged 的內容
git diff --staged

# Commit（訊息格式請遵守 Commit 規範）
git commit -m "feat: Add attention mechanism to policy network"
```

!!! tip "小步 Commit 原則"
    每完成一個獨立的小改動就 commit，不要等到功能全做完才一次 commit。小步 commit 讓你更容易找出問題，也讓隊友更容易 review。

---

## 5. Push 到遠端

```bash
git push
```

Push 之後，你的進度就備份到 GitHub 上了。如果功能已完成，通知主要負責人將你的分支合入 `dev`。

---

## 常用指令速查

```bash
git status                    # 查看目前狀態
git log --oneline -10         # 查看最近 10 個 commit
git diff                      # 查看未 staged 的修改
git diff --staged             # 查看已 staged 的修改
git pull                      # 同步遠端最新進度
git push                      # 上傳本地 commit
git stash                     # 暫存目前修改（臨時切換任務時用）
git stash pop                 # 取回暫存的修改
```

---

**下一步 →** [撰寫與執行測試](testing.md)
