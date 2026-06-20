# Git 與 GitHub 入門

本頁給從未使用過 Git 或 GitHub 的學生看。如果你已經熟悉這兩個工具，可以直接跳過。

---

## Git 是什麼？

Git 是一套**版本控制工具**，安裝在你自己的電腦上。

它解決的問題是：當你寫程式時，怎麼安全地記錄每一次修改、怎麼在出錯時回到上一個狀態、怎麼讓多個人同時修改同一份程式碼而不互相覆蓋。

你可以把 Git 想成「帶有時光機功能的資料夾」：

- 每次執行 `git commit`，Git 就幫你拍一張快照
- 任何時候都可以回到某一張快照
- 多人的快照可以合併（merge）在一起

Git 的所有操作都在你自己的電腦上進行，**不需要網路**。

---

## GitHub 是什麼？

GitHub 是一個**雲端平台**，讓你把 Git 的版本記錄上傳到網路上。

它解決的問題是：Git 本身只在你自己的電腦上，別人看不到。GitHub 讓你的程式碼可以：

- 備份到雲端
- 讓隊友看到並下載
- 讓多人的修改集中在同一個地方合併

| | Git | GitHub |
|---|---|---|
| 是什麼 | 版本控制工具 | 雲端程式碼平台 |
| 安裝在哪 | 你的電腦 | 網路上（不需安裝） |
| 需要網路嗎 | 不需要（commit 在本地） | 需要（push/pull/PR） |
| 主要指令 | `commit`、`merge` | `push`、`pull`、Pull Request |

簡單說：**Git 管版本，GitHub 管協作**。

---

## Clone、Branch、Pull Request 是什麼關係？

這三個詞在加入實驗室專案時會立刻遇到。實驗室**不使用 Fork**，所有人共用同一個 repo，靠分支（Branch）區隔每個人的工作：

**Clone**：把 GitHub 上的 repo 下載到你的電腦。
你 clone 的就是實驗室唯一的 repo 本身（需要先被加入 Collaborator 名單才能 push）。

**Branch**：在同一個 repo 裡開一條獨立的時間線。
你在自己的 `feature/[name]` 分支上修改，不會影響 `dev` 或 `main`，也不會跟其他隊友的分支互相干擾。

**Pull Request（PR）**：請隊友 review 你的分支，並把它合併進 `dev`。
PR 通過後，你的修改才會正式成為整合分支的一部分。

```
實驗室 repo（唯一來源）
  ├── dev 分支
  └── 你的 feature 分支
       ↓  clone
你的電腦（local）
```

---

## 安裝 Git

=== "macOS"
    ```bash
    brew install git
    ```

=== "Linux（Ubuntu/Debian）"
    ```bash
    sudo apt install git
    ```

=== "Windows"
    下載並安裝 [Git for Windows](https://git-scm.com/download/win)。

安裝後設定你的名字和 email（只需做一次）：

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

---

## Git 衝突（Conflict）處理與常用指令

當你與隊友修改了同一個檔案的同一行，並嘗試 `git merge` 或 `git pull` 時，Git 會無法自動判斷該保留誰的修改，進而產生**衝突（Conflict）**。

### 1. 如何識別衝突？
當衝突發生時，終端機通常會顯示：
```
CONFLICT (content): Merge conflict in src/your_project/models/policy.py
Automatic merge failed; fix conflicts and then commit the result.
```
打開衝突的檔案，你會看到 Git 自動插入的衝突標記：
```python
<<<<<<< HEAD
# 這是你在目前分支修改的內容
def compute_reward(distance: float) -> float:
    return -distance * 0.1
=======
# 這是你要合併的分支（例如 dev）上的最新內容
def compute_reward(distance: float) -> float:
    return -distance * 0.5
>>>>>>> dev
```

### 2. 解決衝突三步驟：
1. **人工決定保留哪部分**：手動刪除 `<<<<<<< HEAD`、`=======`、`>>>>>>> dev` 等標記線，並編輯好你想要的程式碼。
2. **Stage 檔案**：編輯完成後，使用 `git add` 告訴 Git 該衝突已解決。
   ```bash
   git add src/your_project/models/policy.py
   ```
3. **完成 Commit**：
   ```bash
   git commit -m "fix: Resolve merge conflict in policy reward scale"
   ```

### 3. 進階時光機與救急指令
* **放棄目前的 Merge/Pull（回到合併前狀態）**：
  ```bash
  git merge --abort
  ```
* **復原某個已被修改的檔案（放棄本地未 stage 的修改）**：
  ```bash
  git checkout -- path/to/file.py
  ```
* **強制回到某個歷史 commit**（會清除該 commit 之後的所有本地修改，慎用！）：
  ```bash
  git reset --hard <commit-hash>
  ```

---

## 延伸學習

→ [Git 與 GitHub 詳細教學簡報](../../assets/git_github_tutorial_zh.pdf)

---

**返回** [快速啟動](../getting_started/contributing.md) | **返回附錄總覽** [附錄總覽](index.md) | **回到手冊主頁** [回到首頁](../index.md)

