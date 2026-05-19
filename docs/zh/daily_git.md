# 日常 Git 操作

本頁說明在 AGILAB 實驗室中，每天開發時會用到的 Git 操作。如果你還不熟悉 Git 的基本概念，可以先把這頁當成查詢手冊，遇到情況再回來對照。

---

## 每日工作流程

典型的一天開發流程如下：

```
同步最新程式碼 → 開新分支 → 寫程式 → Commit → Push → 開 PR
```

---

## 1. 同步最新程式碼

開始工作前，先確保 `dev` 分支是最新的：

```bash
git checkout dev
git pull origin dev
```

---

## 2. 建立功能分支

每個新功能或實驗都應在獨立的分支上進行，**不要直接在 `dev` 上開發**：

```bash
# 格式：feature/[描述]
git checkout -b feature/add-attention-layer
```

分支命名建議：

| 情境 | 範例 |
|---|---|
| 新功能 / 模型 | `feature/lstm-memory` |
| 修 Bug | `fix/reward-overflow` |
| 實驗調參 | `exp/lr-sweep-3e4` |
| 文件更新 | `docs/update-readme` |

---

## 3. 查看目前狀態

隨時用 `git status` 確認哪些檔案被修改了：

```bash
git status
```

輸出範例：

```
On branch feature/add-attention-layer
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
# 第一次 push 這個分支（建立追蹤）
git push -u origin feature/add-attention-layer

# 之後的 push
git push
```

---

## 6. 開 Pull Request（PR）

Push 之後，到 GitHub 上：

1. 點擊 **"Compare & pull request"**
2. **Base branch 選 `dev`**（不是 `main`）
3. 在描述中填寫：
   - **做了什麼**：新增 attention 機制
   - **為什麼**：改善長序列的記憶能力
   - **怎麼測試**：執行了 `pytest tests/test_policy.py`，訓練曲線如附圖

---

## 7. 保持分支與 dev 同步

開發期間如果 `dev` 有其他人的更新，需要同步進來：

```bash
# 切到 dev 更新
git checkout dev
git pull origin dev

# 切回自己的分支，將 dev 的更新合進來
git checkout feature/add-attention-layer
git merge dev
```

---

## 處理 Merge Conflict

當兩個人修改了同一個檔案的同一行，就會發生 conflict。

```bash
# merge 後若有衝突，git 會告訴你：
Auto-merging src/your_project/models/policy.py
CONFLICT (content): Merge conflict in src/your_project/models/policy.py
```

**步驟：**

1. 打開衝突的檔案，會看到標記：

```python
<<<<<<< HEAD
    hidden_dim = 256  # 你的版本
=======
    hidden_dim = 512  # dev 分支的版本
>>>>>>> dev
```

2. 手動編輯，選擇保留哪個版本（或合併兩者）：

```python
    hidden_dim = 512  # 決定用 dev 的版本
```

3. 刪除所有 `<<<<<<<`、`=======`、`>>>>>>>` 標記，然後 commit：

```bash
git add src/your_project/models/policy.py
git commit -m "fix: Resolve merge conflict in policy hidden_dim"
```

---

## 常用指令速查

```bash
git status                    # 查看目前狀態
git log --oneline -10         # 查看最近 10 個 commit
git diff                      # 查看未 staged 的修改
git diff --staged             # 查看已 staged 的修改
git stash                     # 暫存目前修改（切換分支前用）
git stash pop                 # 取回暫存的修改
git branch                    # 列出所有本地分支
git checkout -                # 切換到上一個分支
```

---

**下一步 →** [撰寫與執行測試](testing.md)
