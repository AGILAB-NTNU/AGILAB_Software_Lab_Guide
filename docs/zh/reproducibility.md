# 研究可重現性指南 (Research Reproducibility Guide)

在研究中，「可重現性」是科學嚴謹性的基石。為了確保您的實驗結果在不同機器、不同時間都能被精確重現，請務必遵守以下規範。

## 1. 隨機種子鎖定 (Seed Everything)

隨機性是導致實驗結果產生波動的主要原因。我們要求在所有進入點（如 `train.py`, `eval.py`）的開頭使用統一的種子鎖定工具。

### 標準鎖定函式範例
建議在 `src/project_name/utils/reproducibility.py` 中實作以下函式：

```python
import random
import os
import numpy as np
import torch

def seed_everything(seed: int = 42):
    """固定所有可能的隨機來源以確保實驗可重現性。"""
    random.seed(seed)
    os.environ['PYTHONHASHSEED'] = str(seed)
    np.random.seed(seed)
    torch.manual_seed(seed)
    torch.cuda.manual_seed(seed)
    torch.cuda.manual_seed_all(seed)  # 如果使用多個 GPU
    
    # 確保 PyTorch 運算使用決定性演算法 (可能會稍微降低性能)
    torch.backends.cudnn.deterministic = True
    torch.backends.cudnn.benchmark = False
    
    print(f"Random seed set to: {seed}")
```

## 2. 硬體與演算法的決定性 (Determinism)

即使鎖定了種子，某些深度學習運算（如 `conv` 或 `atomicAdd`）在 CUDA 上預設可能是非決定性的。

*   **CUDA 決定性**：設定 `torch.use_deterministic_algorithms(True)`。這會強制 PyTorch 在找不到決定性演算法時報錯，提醒您注意潛在的不一致性。
*   **多執行緒影響**：在 `DataLoader` 中設定 `num_workers > 1` 可能會引入非決定性的數據讀取順序。若需嚴格重現，建議在除錯階段將 `num_workers` 設為 `0`。

## 3. 環境版本控制

我們嚴禁手動安裝套件而不更新設定檔。

*   **Conda 環境**：所有的依賴套件必須記錄在 `environment.yml` 中。
*   **Python 打包**：專案的核心依賴必須記錄在 `pyproject.toml` 的 `dependencies` 區塊。
*   **硬體紀錄**：在論文或實驗日誌中，請標註使用的 GPU 型號與 CUDA 版本。

## 4. 實驗追蹤 (Experiment Tracking)

我們強烈建議使用 **Weights & Biases (W&B)** 或 **MLflow** 來紀錄每一次 Run 的所有細節：
*   所有超參數 (Hyperparameters)。
*   訓練期間的指標 (Metrics)。
*   使用的程式碼版本 (Git Hash)。
*   模型權重 (Artifacts)。

> [!IMPORTANT]
> **沒有 Git Hash 的實驗結果是不具備科學公信力的。** 請務必在 Commit 程式碼後再啟動正式實驗。

## 5. 資料集版本化 (Dataset Versioning)

*   **固定分割 (Fixed Split)**：嚴禁在程式碼中動態進行 `train_test_split`。應預先產生 `train.txt` 與 `test.txt` 檔案來指定索引。
*   **檢查碼**：對於關鍵資料集，請保留其 MD5 或 SHA-256 檢查碼。
