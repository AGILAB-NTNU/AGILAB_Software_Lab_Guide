# tmux 無 sudo 安裝教學

在實驗室伺服器上，通常沒有 `sudo` 權限，無法用 `apt install tmux`。以下介紹透過 **Conda** 安裝 tmux 的方式，不需要任何管理員權限。

---

## 安裝

確認 conda 環境已啟動後，執行：

```bash
conda activate agilab_env
conda install -c conda-forge tmux
```

安裝完成後驗證：

```bash
tmux -V
# 輸出類似：tmux 3.3a
```

---

## 基本操作

### 建立 session

```bash
# 建立並進入一個新的 session（取個好記的名字）
tmux new -s train
```

### 離開 session（程式繼續在背景跑）

在 tmux 內按：

```
Ctrl+B  →  放開  →  D
```

這個動作叫做 **detach**，程式不會停止，你只是離開了畫面。

### 重新連回 session

```bash
tmux attach -t train
```

### 查看目前所有 session

```bash
tmux ls
```

輸出範例：

```
train: 1 windows (created Wed May  1 14:32:01 2024)
eval:  1 windows (created Wed May  1 16:00:00 2024)
```

### 結束 session

在 session 內，直接讓程式跑完，或手動輸入：

```bash
exit
```

---

## 常用快捷鍵整理

所有快捷鍵都以 **`Ctrl+B`** 開頭，放開後再按下一個鍵：

| 按鍵 | 功能 |
|---|---|
| `Ctrl+B` → `D` | Detach（離開 session，程式繼續跑） |
| `Ctrl+B` → `[` | 進入捲動模式（可以向上看 log，按 `Q` 離開） |
| `Ctrl+B` → `%` | 水平切割視窗 |
| `Ctrl+B` → `"` | 垂直切割視窗 |
| `Ctrl+B` → 方向鍵 | 切換視窗格 |

!!! tip "最常用的就兩個"
    日常使用只需記得：`Ctrl+B D`（離開）和 `tmux attach -t [名稱]`（回來）。

---

**返回附錄總覽** [附錄總覽](index.md) | **回到手冊主頁** [回到首頁](../index.md)
