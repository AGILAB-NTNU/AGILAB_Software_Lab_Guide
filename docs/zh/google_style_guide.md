# Google Python Style Guide 要點

!!! info "這是什麼？"
    [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html) 是 Google 內部使用的 Python 程式碼風格規範，被廣泛採用為業界標準。實驗室以此為基礎，搭配 **Ruff** 自動強制執行大部分規則。

## 為什麼要有程式碼風格？

同一份程式碼可能由多人在不同時間閱讀和修改。一致的風格讓人能快速讀懂別人的程式碼，減少認知負擔。

---

## 實驗室最常用到的規則

### 1. 命名規範

| 對象 | 規則 | 範例 |
|---|---|---|
| 函式 / 變數 | `snake_case` | `compute_reward`, `obs_dim` |
| 類別 | `PascalCase` | `ReplayBuffer`, `PolicyNetwork` |
| 常數 | `UPPER_SNAKE_CASE` | `MAX_EPISODE_STEPS = 1000` |
| 私有成員 | 單底線開頭 | `_normalize`, `self._buffer` |
| 模組檔案 | `snake_case.py` | `reward_shaping.py` |

```python
# ❌ 不符合規範
def ComputeReward(Obs, Scale):
    maxSteps = 1000
    return Obs * Scale

# ✅ 符合規範
MAX_STEPS = 1000

def compute_reward(obs: float, scale: float) -> float:
    return obs * scale
```

---

### 2. 行長與換行

- 每行**不超過 88 個字元**（Ruff 預設值，略寬於原版的 80）
- 函式參數過多時，每個參數獨立一行：

```python
# ❌ 超出行長
def train_agent(env_name, num_episodes, learning_rate, gamma, clip_ratio, device):

# ✅ 每個參數獨立一行
def train_agent(
    env_name: str,
    num_episodes: int,
    learning_rate: float,
    gamma: float = 0.99,
    clip_ratio: float = 0.2,
    device: str = "cuda",
) -> None:
```

---

### 3. 引號

統一使用**雙引號** `"`（Ruff 會自動轉換）：

```python
# ✅
name = "humanoid"
message = "Training started"

# ❌ 單引號（Ruff 會自動修正，但養成習慣更好）
name = 'humanoid'
```

---

### 4. Import 順序

Import 分三組，組間空一行，Ruff 會自動排序（`I` 規則）：

```python
# 第一組：標準函式庫
import os
import random
from typing import Optional

# 第二組：第三方套件
import numpy as np
import torch
import torch.nn as nn

# 第三組：本專案模組
from your_project.models import PolicyNetwork
from your_project.utils import seed_everything
```

---

### 5. 避免萬用 Import

```python
# ❌ 禁止（會汙染命名空間，難以追蹤來源）
from numpy import *
from torch.nn import *

# ✅ 明確指定
import numpy as np
from torch.nn import Linear, ReLU
```

---

### 6. 例外處理

捕捉具體的例外類型，不要用裸 `except`：

```python
# ❌ 會捕捉所有例外，包括 KeyboardInterrupt
try:
    load_checkpoint(path)
except:
    pass

# ✅ 只捕捉預期的例外
try:
    load_checkpoint(path)
except FileNotFoundError:
    logger.warning("Checkpoint not found, training from scratch.")
```

---

### 7. 不要用可變物件當預設參數

```python
# ❌ 所有呼叫共享同一個 list，會產生難以察覺的 bug
def add_result(value, results=[]):
    results.append(value)
    return results

# ✅ 用 None 作為哨兵值
def add_result(value: float, results: list | None = None) -> list:
    if results is None:
        results = []
    results.append(value)
    return results
```

---

## Ruff 自動處理的部分

實驗室的 `pyproject.toml` 已啟用以下規則，**commit 前執行 `ruff check . --fix && ruff format .` 即可自動修正**：

| 規則代碼 | 涵蓋內容 |
|---|---|
| `E` | 基本語法與格式錯誤 |
| `F` | 未使用的 import、未定義的變數 |
| `W` | 警告（如尾端空白） |
| `I` | Import 排序 |
| `N` | 命名規範（PascalCase、snake_case 等） |

```bash
# 自動修正可修正的問題
ruff check . --fix

# 統一格式（引號、縮排、行長等）
ruff format .
```

!!! tip
    建議安裝 pre-commit hook，讓每次 `git commit` 前自動執行 Ruff：
    ```bash
    pre-commit install
    ```

---

**返回** [快速啟動](contributing.md) | **附錄首頁** [回到首頁](index.md)
