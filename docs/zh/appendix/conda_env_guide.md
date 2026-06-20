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

新增套件時，**只需要更新 `environment.yml`**，不需要動 `pyproject.toml` 的相依套件列表。

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

## Conda 進階調優與避坑指南

### 1. 為什麼我的 Conda 相依性解析（Solving environment）特別慢？
Conda 在安裝套件時，會交叉比對所有已安裝套件與 channels 上的版本相容性。以下兩個原因會造成解析極度緩慢：
- **Channels 順序與優先度衝突**：若同時引入了順序不對的 `defaults` 與 `conda-forge`，Conda 會跨來源尋找最佳相容版本。請確保你的 `environment.yml` 中的 channels 優先順序如範例所示（優先使用 `pytorch` / `nvidia` 與 `conda-forge`，最後才是 `defaults`）。
- **解析引擎效率限制**：若 Conda 的相依性關係圖過於複雜，建議使用 `mamba`（使用 C++ 撰寫的極速解析器）來替代 Conda 的解析核心：
  ```bash
  conda install mamba -n base -c conda-forge
  mamba env update -f environment.yml --prune
  ```

### 2. 清理環境快取，釋放磁碟空間
Conda 會在本地快取下載過的套件壓縮檔（`.tar.bz2`/`.conda`）與解壓後的暫存檔案，長期累積可能佔用數十 GB 空間。使用以下指令可以安全清理：
```bash
# 清理所有未使用的快取包與索引目錄
conda clean --all
```

### 3. 導出乾淨的 environment.yml（跨平台相容）
當你完成實驗，想把目前的環境分享給別人時，若直接執行 `conda env export`，會包含許多本地系統與硬體特有的雜亂相依套件。正確的「瘦身與跨平台」導出指令為：
```bash
# 只導出你主動安裝的套件（從歷史記錄中），不包含底層相依套件與平台特定路徑
conda env export --from-history > environment_clean.yml
```

---

**返回** [環境管理](../getting_started/conda_guide.md) | **返回附錄總覽** [附錄總覽](index.md) | **回到手冊主頁** [回到首頁](../index.md)
