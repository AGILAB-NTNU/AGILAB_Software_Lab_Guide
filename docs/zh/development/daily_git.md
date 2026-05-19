# 日常 Git 操作

> 還沒做過初始化？請先完成 [快速啟動](../getting_started/contributing.md) 的 Fork、Clone 與 upstream 設定。

本頁說明初始化完成後的日常開發流程。

---

## 分支架構

```
AGILAB-NTNU/project（實驗室 repo）
  ├── main   ← 專案正式結束後才合併（由老師決定）
  └── dev    ← 整合分支，接受所有人的 PR

YOUR_NAME/project（你的 fork）
  └── dev    ← 你的開發分支，完成後發 PR 到上方的 dev
```

---

## 每日工作流程

```
同步上游 → 寫程式 → Commit → Push → （功能完成時）發 PR
```

---

## 1. 每天開始前：同步上游

把實驗室最新進度合入自己的 fork，避免累積衝突：

```bash
git fetch upstream
git merge upstream/dev
```

---

## 2. 查看目前狀態

```bash
git status
```

輸出範例：

```
Changes not staged for commit:
  modified:   src/your_project/models/policy.py

Untracked files:
  src/your_project/models/attention.py
```

- **modified**：已存在的檔案被修改
- **Untracked**：新建的檔案，Git 還沒追蹤

---

## 3. Commit

只 stage 你修改過的相關檔案，不要一次 `git add .` 全加：

```bash
git add src/your_project/models/policy.py
git diff --staged                          # 確認 staged 的內容
git commit -m "feat: Add attention mechanism to policy network"
```

!!! tip "小步 Commit 原則"
    每完成一個獨立的小改動就 commit，不要等到功能全做完才一次 commit。小步 commit 讓你更容易找出問題，也讓隊友更容易 review。

---

## 4. Push

```bash
git push
```

---

## 5. 功能完成時：發 Pull Request

1. 前往你的 GitHub fork 頁面，點選 **Compare & pull request**
2. 確認方向：`YOUR_NAME/project:dev` → `AGILAB-NTNU/project:dev`
3. 填寫 PR 標題（同 Commit 規範格式）與簡短說明
4. 送出後通知主要負責人 review

!!! note "PR 不需要等功能 100% 完整"
    想請隊友確認方向時，可以開 Draft PR，標題加上 `[WIP]`。

---

## 常用指令速查

```bash
git status                 # 查看目前狀態
git log --oneline -10      # 查看最近 10 個 commit
git diff                   # 查看未 staged 的修改
git diff --staged          # 查看已 staged 的修改
git fetch upstream         # 抓取實驗室最新進度
git merge upstream/dev     # 將實驗室 dev 合入目前分支
git push                   # 上傳本地 commit 到自己的 fork
git stash                  # 暫存目前修改（臨時切換任務時用）
git stash pop              # 取回暫存的修改
```

---

**下一步 →** [Git Commit 規範](../getting_started/git_convention.md)
