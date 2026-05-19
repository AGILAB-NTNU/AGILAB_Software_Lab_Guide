# Conda 與 environment.yml 說明

## 為什麼需要 Conda？

研究專案通常需要 PyTorch、CUDA、特定版本的物理模擬器等高度依賴硬體與版本的套件。直接在系統 Python 安裝容易造成：

- **版本衝突**：不同專案需要不同版本的 PyTorch，裝在同一個環境互相干擾
- **環境汙染**：實驗裝了一大堆套件，六個月後不知道哪些是必要的
- **無法重現**：隊友或伺服器上的 Python 版本不同，程式跑不起來

Conda 讓每個專案有自己獨立的環境，彼此完全隔離，也能精確記錄「這個專案跑在什麼環境」。

---

## environment.yml 是什麼？

`environment.yml` 是 Conda 環境的「配方」，完整描述這個專案需要哪些套件與版本：

```yaml
name: agilab_env          # 環境名稱，conda activate 時使用
channels:
  - pytorch               # 套件來源（優先順序由上到下）
  - nvidia
  - conda-forge
  - defaults
dependencies:
  - python=3.9
  - pytorch=2.2.1
  - pytorch-cuda=12.1
  - numpy
  - pip:
    - hydra-core          # conda 找不到的套件，改用 pip 安裝
    - -e .[dev]           # 以開發模式安裝本專案
```

---

## 日常維護：新增套件

新增套件時，**只需要更新 `environment.yml`**，不需要動 `pyproject.toml` 的依賴列表。

```bash
# 1. 在 environment.yml 的 dependencies 或 pip 區塊加入新套件
# 2. 更新目前的環境
conda env update -f environment.yml --prune
```

更新後通知隊友，讓大家同步執行 `conda env update`，確保所有人的環境一致。

!!! warning "不要只用 conda install 或 pip install"
    直接安裝的套件不會記錄到 `environment.yml`，隊友無法重現你的環境。每次安裝新套件，都要同步更新 `environment.yml`。

---

## 從零建立環境

```bash
# 第一次設定（clone 專案後執行一次）
conda env create -f environment.yml

# 啟動環境（每次開始工作前）
conda activate agilab_env

# 確認安裝正確
python -c "import torch; print(torch.__version__)"
```

---

## 與 pyproject.toml 的分工

這兩個檔案都與套件有關，但職責不同，**日常只需要維護 `environment.yml`**：

| | `environment.yml` | `pyproject.toml` |
|---|---|---|
| 管理工具 | conda | pip |
| 主要用途 | 建立與重現執行環境 | 讓專案可被 import 與發布 |
| 何時修改 | 每次新增套件 | 初始化時改一次名稱即可 |
| 新手需要管嗎 | ✅ 是 | 僅改名稱 |

→ 詳見 [pyproject.toml 說明](pyproject_guide.md)

---

**附錄首頁** [回到首頁](../index.md)
