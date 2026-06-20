# 研究可重現性

在研究中，「可重現性」是科學嚴謹性的基石。為了確保你的實驗結果在不同機器、不同時間都能被精確重現，請務必遵守以下規範。

## 1. 隨機種子鎖定

隨機性是導致實驗結果產生波動的主要原因。我們要求在所有進入點（如 `train.py`, `eval.py`）的開頭使用統一的種子鎖定工具。

### 標準鎖定函式範例
建議在 `src/project_name/utils/reproducibility.py` 中實作以下函式：

```python
import random
import os
import numpy as np
import torch

def seed_everything(seed: int = 42) -> None:
    """Lock all random sources to ensure experiment reproducibility.

    Args:
        seed: Integer seed value applied globally across all frameworks.
            Defaults to 42.
    """
    random.seed(seed)
    os.environ['PYTHONHASHSEED'] = str(seed)
    np.random.seed(seed)
    torch.manual_seed(seed)
    torch.cuda.manual_seed(seed)
    torch.cuda.manual_seed_all(seed)

    torch.backends.cudnn.deterministic = True
    torch.backends.cudnn.benchmark = False

    import logging
    logging.getLogger(__name__).info("Random seed set to: %d", seed)
```

## 2. 硬體與演算法的決定性

即使鎖定了種子，某些深度學習運算（如 `conv` 或 `atomicAdd`）在 CUDA 上預設可能是非決定性的。

*   **CUDA 決定性**：設定 `torch.use_deterministic_algorithms(True)`。這會強制 PyTorch 在找不到決定性演算法時報錯，提醒您注意潛在的不一致性。
*   **多執行緒影響**：在 `DataLoader` 中設定 `num_workers > 1` 可能會引入非決定性的資料讀取順序。若需嚴格重現，建議在除錯階段將 `num_workers` 設為 `0`。

## 3. 環境版本控制

我們嚴禁手動安裝套件而不更新設定檔。

*   **Conda 環境**：所有的依賴套件必須記錄在 `environment.yml` 中。
*   **Python 打包**：專案的核心依賴必須記錄在 `pyproject.toml` 的 `dependencies` 區塊。
*   **硬體紀錄**：在論文或實驗日誌中，請標註使用的 GPU 型號與 CUDA 版本。

## 4. 資料集版本化

**固定分割**：嚴禁在程式碼中動態進行 `train_test_split`。應預先將資料集分割成固定的資料夾。不同的前處理版本以獨立資料夾區隔，與 `processed/` 同層存放：

```
data/
├── raw/                      ← 原始資料，不可修改
├── processed/                ← 預設版本
│   ├── train/
│   ├── eval/                 （選用）
│   └── test/
└── processed_normalized/     ← 其他前處理版本
    ├── train/
    └── test/
```

每個資料夾一旦建立後不應再更動。在設定檔中明確指定使用哪個版本，確保所有人使用相同的資料：

```yaml
# configs/ppo_humanoid.yaml
data:
  processed_dir: "data/processed"
```

---

**下一步 →** [專案生命週期](lifecycle.md)
