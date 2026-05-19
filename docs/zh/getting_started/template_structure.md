# 專案模板結構說明

本頁說明 [SoftwareTemplate](https://github.com/AGILAB-NTNU/SoftwareTemplate) 中每個資料夾與設定檔的用途，幫助你快速了解「什麼東西應該放在哪裡」。

## 完整目錄結構

```
your_project/
│
├── src/project_name/         # 核心 Python 套件（⚠ 需重命名）  【必要】
│   └── __init__.py
│
├── tests/                    # pytest 測試套件                 【必要】
│
├── configs/                  # 實驗設定檔（YAML）               【建議】
│
├── data/                                                        【建議】
│   ├── raw/                  # 原始資料（唯讀，不做修改）
│   └── processed/            # 預處理後的資料
│
├── scripts/                  # Bash / SLURM 執行腳本           【建議】
│
├── notebooks/                # Jupyter 探索性分析               【選用】
│
├── docker/                   # 容器化部署設定                   【選用】
│
├── docs/                     # MkDocs 文件站原始碼              【選用】
│   └── assets/
│
├── environment.yml           # Conda 環境定義                   【必要】
├── pyproject.toml            # 套件設定 + Ruff 程式碼規範       【必要】
├── README.md                 # 專案說明                         【必要】
└── mkdocs.yml                # 文件站設定                       【選用】
```

| 分類 | 說明 |
|---|---|
| **必要** | 每個專案都需要，clone 後立刻設定 |
| **建議** | 研究常用，依專案性質決定是否採用 |
| **選用** | 有特定需求才啟用 |

---

## 各目錄說明

### `src/project_name/` — 核心程式碼 `必要`

這是整個專案最重要的目錄，存放所有可重複使用的 Python 模組。

!!! warning "初始化時必須重命名"
    Clone 專案後，請立刻將 `src/project_name/` 重命名為你的實際專案名稱（例如 `src/humanoid_rl/`），並同步修改 `pyproject.toml` 中的 `name = "project_name"`。

建議的子目錄結構：

```
src/your_project/
├── __init__.py
├── models/           # 神經網路架構定義
├── envs/             # 環境包裝器（Gym/Isaac Gym）
├── training/         # 訓練迴圈、損失函式
├── evaluation/       # 評估腳本
└── utils/            # 通用工具（seed, logging, 等）
```

---

### `configs/` — 實驗設定檔 `建議`

存放所有超參數與實驗設定，格式為 YAML。好處是讓「跑什麼實驗」與「程式碼邏輯」分離，不需要動程式碼就能切換設定。

```yaml
# configs/train_ppo.yaml
seed: 42
lr: 3e-4
gamma: 0.99
max_steps: 1000000
```

用 Python 標準函式庫即可讀取：

```python
import yaml

with open("configs/train_ppo.yaml") as f:
    cfg = yaml.safe_load(f)

print(cfg["lr"])  # 0.003
```

---

### `data/` — 資料集 `建議`

| 子目錄 | 用途 |
|---|---|
| `data/raw/` | 原始資料，**永遠不要修改**，保留為唯讀備份 |
| `data/processed/` | 清洗、格式轉換後的資料，由腳本自動產生 |

!!! note
    `data/` 已被加入 `.gitignore`，大型資料集請存放在伺服器指定路徑，不要 commit 進 Git。

---

### `scripts/` — 執行腳本 `建議`

存放用於啟動訓練、評估、資料預處理的 Bash 或 SLURM 腳本。讓長指令不用每次手打：

```bash
# scripts/train_ppo.sh
#!/bin/bash
#SBATCH --gres=gpu:1
#SBATCH --time=24:00:00

conda activate agilab_env
python -m your_project.training.train --config configs/train_ppo.yaml
```

---

### `notebooks/` — Jupyter 探索筆記 `選用`

用於探索性資料分析（EDA）、視覺化實驗結果、快速驗證想法。**核心邏輯不應寫在 notebook 裡**，驗證後應移入 `src/`。

---

### `tests/` — 自動化測試 `必要`

存放 pytest 測試，至少應覆蓋 `src/` 中的核心邏輯。建議在 push 前於本地執行確認。

```bash
# 在本地執行所有測試
pytest tests/
```

→ 詳見 [撰寫與執行測試](../development/testing.md)

---

### `docker/` — 容器化部署 `選用`

存放 `Dockerfile` 與 `docker-compose.yml`，用於在不同伺服器或雲端環境重現完整的執行環境。一般開發期間不需要用到。

→ 詳見 [Docker 容器化部署](../appendix/docker_guide.md)

---

### `docs/` — 文件站 `選用`

存放 MkDocs 文件站的 Markdown 原始碼。執行 `mkdocs serve` 可在本地預覽。

---

## 重要設定檔

### `environment.yml` — Conda 環境 `必要`

定義所有依賴套件，確保不同成員的環境一致。**新增套件時必須更新此檔案。**

```bash
# 建立環境
conda env create -f environment.yml

# 更新環境（環境已存在時）
conda env update -f environment.yml --prune
```

→ 詳見 [Conda 與 environment.yml 說明](../appendix/conda_env_guide.md)

### `pyproject.toml` — 套件與程式碼規範 `必要`

集中管理兩件事：

1. **Python 套件設定**（名稱、版本、依賴）
2. **Ruff 程式碼風格規範**（行長 88、雙引號、啟用 E/F/W/I/N 規則）

初始化時只需修改 `[project]` 區塊的 `name` 與 `description`，Ruff 設定維持預設即可。

```toml
[project]
name = "humanoid_rl"                    # ← 改成你的專案名稱
description = "Humanoid locomotion with PPO"  # ← 一句話描述
version = "0.1.0"                       # 維持不動
```

→ 詳見 [pyproject.toml 說明](../appendix/pyproject_guide.md)

---

**下一步 →** [日常 Git 操作](../development/daily_git.md)
