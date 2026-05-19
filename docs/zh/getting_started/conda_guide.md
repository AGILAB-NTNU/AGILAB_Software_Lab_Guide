# 環境管理

本頁說明實驗室統一的環境管理流程，以及 `environment.yml` 和 `pyproject.toml` 各自負責什麼。

---

## environment.yml 和 pyproject.toml 各是做什麼的？

加入新專案時，你會看到兩個跟依賴有關的檔案，初次看到可能會困惑：

| 檔案 | 負責什麼 | 日常維護 |
|---|---|---|
| `environment.yml` | 整個執行環境：Python 版本、CUDA、PyTorch、所有套件 | **新增套件時更新這裡** |
| `pyproject.toml` | 這個 Python 套件本身的 metadata 與 Ruff 程式碼規範 | 通常只在初始化時改一次 |

**一句話記住**：日常開發時，只需要維護 `environment.yml`。

`pyproject.toml` 的詳細說明 → [pyproject.toml 說明](../appendix/pyproject_guide.md)

---

## 為什麼要用 Conda？

研究專案通常有複雜的依賴（CUDA 版本、物理模擬器、特定版本的 PyTorch），如果每個人直接在自己的電腦上手動安裝，很容易出現「在我的電腦上可以跑」的問題。

Conda 讓你把整個環境的依賴寫進 `environment.yml`，任何人只要一行指令就能重現完全相同的環境：

```bash
conda env create -f environment.yml
```

---

## environment.yml 的結構

```yaml
name: agilab_env          # conda 環境名稱

channels:
  - pytorch               # 安裝 PyTorch 的來源
  - nvidia                # 安裝 CUDA 相關套件的來源
  - conda-forge           # 通用套件來源
  - defaults

dependencies:
  - python=3.9            # Python 版本
  - pytorch=2.2.1         # PyTorch 版本
  - pytorch-cuda=12.1     # CUDA 版本（依機器的驅動決定）
  - pip                   # 讓下方的 pip 區塊可以運作

  - pip:
    - -e .                # 將這個專案本身安裝為可編輯套件
    - numpy
    - pyyaml
```

- `channels`：告訴 conda 去哪裡找套件，順序代表優先度
- `dependencies`：實際安裝的套件清單
- `pip` 區塊：無法用 conda 安裝的套件，或專案本身（`-e .`）

---

## 常用指令

```bash
# 第一次建立環境
conda env create -f environment.yml

# 新增套件後更新環境
conda env update -f environment.yml --prune

# 啟動環境
conda activate agilab_env

# 查看已安裝的套件
conda list
```

!!! warning "新增套件時，請同時更新 environment.yml"
    直接跑 `pip install xxx` 或 `conda install xxx` 而不更新 `environment.yml`，
    會導致隊友的環境和你不一致。正確做法是先加進 `environment.yml`，再執行 `conda env update`。

---

## 處理 CUDA 版本

為了確保 GPU 加速能正常運作，`environment.yml` 中需要明確指定 `pytorch-cuda` 版本：

```yaml
dependencies:
  - pytorch-cuda=12.1  # 依你的機器驅動版本決定
```

執行 `nvidia-smi` 可以看到你的驅動支援的最高 CUDA 版本。

!!! warning "「程式碼能跑」不代表「結果一致」"
    若發現實驗結果在不同顯卡上有顯著差異，請檢查不同顯卡的 Float32 運算精度與 Tensor Cores 的調用情況，並在 `README.md` 中註明實驗使用的 GPU 型號與驅動版本。

---

## 疑難排解

### 依賴解析過慢

```bash
conda install mamba -n base -c conda-forge
mamba env create -f environment.yml   # 用 mamba 取代 conda，速度快很多
```

### 物理模擬器（如 Isaac Gym / MuJoCo）

對於無法透過 conda 直接安裝的模擬器：

1. 在專案根目錄建立 `third_party/` 資料夾
2. 將模擬器源碼放入其中
3. 在 `environment.yml` 的 `pip` 區塊加入 `-e third_party/xxx`

---

**下一步 →** [模板結構說明](template_structure.md)
