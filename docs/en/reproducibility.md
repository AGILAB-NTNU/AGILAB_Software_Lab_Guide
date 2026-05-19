# Research Reproducibility Guide

In AGI, Reinforcement Learning, and Robotics research, **reproducibility** is the cornerstone of scientific rigor. To ensure your experimental results can be precisely reproduced across different machines and times, please strictly adhere to the following guidelines.

## 1. Seed Everything

Stochasticity is a major cause of variance in experimental results. We require the use of a unified seed-locking utility at the beginning of all entry points (e.g., `train.py`, `eval.py`).

### Standard Seed Locking Function
We recommend implementing the following function in `src/project_name/utils/reproducibility.py`:

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
    torch.cuda.manual_seed_all(seed)  # For multi-GPU setups

    # Force deterministic algorithms; raises error if unavailable
    torch.backends.cudnn.deterministic = True
    torch.backends.cudnn.benchmark = False

    print(f"Random seed set to: {seed}")
```

## 2. Hardware and Algorithmic Determinism

Even with locked seeds, certain deep learning operations (like `conv` or `atomicAdd`) on CUDA can be non-deterministic by default.

*   **CUDA Determinism**: Set `torch.use_deterministic_algorithms(True)`. This forces PyTorch to error out if a deterministic algorithm is not available, alerting you to potential inconsistencies.
*   **Multi-threading Impact**: Setting `num_workers > 1` in `DataLoader` can introduce non-deterministic data loading orders. For strict reproduction, set `num_workers = 0` during debugging.

## 3. Environment Versioning

Manual installation of packages without updating configuration files is strictly prohibited.

*   **Conda Environment**: All dependencies must be recorded in `environment.yml`.
*   **Python Packaging**: Core dependencies must be recorded in the `dependencies` section of `pyproject.toml`.

## 4. Experiment Tracking

We strongly recommend using **Weights & Biases (W&B)** or **MLflow** to record every run:
*   Hyperparameters.
*   Training metrics.
*   Code version (Git Hash).
*   Model weights (Artifacts).

!!! warning "Experimental results without a Git Hash lack scientific credibility"
    Always commit your code before starting formal experiments.
