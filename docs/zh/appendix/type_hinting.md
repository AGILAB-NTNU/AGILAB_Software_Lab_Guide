# Type Hinting 入門

!!! info "這是什麼？"
    Type Hinting（型別提示）是 Python 3.5+ 的語法，讓你在函式簽名中標注參數與回傳值的型別。它不會改變程式的執行行為，但能讓程式碼更易讀、讓 IDE 提供更好的補全與錯誤提示。

## 為什麼實驗室要求 Type Hinting？

在研究程式碼中，你常常幾個月後才回來看自己寫的東西。沒有型別標注的函式很難一眼看懂「這個參數應該傳什麼」。

## 基本語法

### 函式參數與回傳值

```python
# 沒有 Type Hinting（不符合規範）
def compute_reward(obs, scale):
    return obs * scale

# 有 Type Hinting（符合規範）
def compute_reward(obs: float, scale: float) -> float:
    return obs * scale
```

### 常見型別

| 型別 | 說明 | 範例 |
|---|---|---|
| `int` | 整數 | `epoch: int = 100` |
| `float` | 浮點數 | `lr: float = 3e-4` |
| `str` | 字串 | `name: str` |
| `bool` | 布林值 | `use_cuda: bool = True` |
| `list[int]` | 整數 list | `indices: list[int]` |
| `dict[str, float]` | 字典 | `metrics: dict[str, float]` |
| `np.ndarray` | NumPy 陣列 | `obs: np.ndarray` |
| `torch.Tensor` | PyTorch 張量 | `action: torch.Tensor` |

### Optional 與 None

```python
from typing import Optional

# 參數可以是 float 或 None
def train(checkpoint: Optional[str] = None) -> None:
    ...
```

Python 3.10+ 可以用更簡短的寫法：

```python
def train(checkpoint: str | None = None) -> None:
    ...
```

## 實驗室規範範例

```python
import numpy as np
import torch
from typing import Optional

def seed_everything(seed: int = 42) -> None:
    """Lock all random sources."""
    ...

def load_checkpoint(
    path: str,
    device: torch.device,
    strict: bool = True,
) -> dict[str, torch.Tensor]:
    """Load model weights from a checkpoint file."""
    ...
```

---

**返回** [快速啟動](../getting_started/contributing.md) | **附錄首頁** [回到首頁](../index.md)
