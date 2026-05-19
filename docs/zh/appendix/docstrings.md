# Google Style Docstrings

!!! info "這是什麼？"
    Docstring 是寫在函式、類別或模組**第一行**的說明字串（用三個雙引號包住）。Google Style 是實驗室採用的格式標準。

## 為什麼需要 Docstring？

當你用 IDE 把滑鼠移到函式上方，或是其他人用 `help(你的函式)` 查詢時，他們看到的就是 Docstring。良好的 Docstring 讓隊友不需要閱讀實作細節就能使用你的函式。

## 標準格式

```python
def compute_advantage(
    rewards: list[float],
    values: list[float],
    gamma: float = 0.99,
    lam: float = 0.95,
) -> list[float]:
    """Compute Generalized Advantage Estimation (GAE).

    Args:
        rewards: List of per-step rewards from the environment.
        values: List of state-value estimates from the critic.
        gamma: Discount factor for future rewards.
        lam: GAE lambda, controls bias-variance tradeoff.

    Returns:
        List of advantage estimates, same length as rewards.

    Raises:
        ValueError: If rewards and values have different lengths.
    """
    if len(rewards) != len(values):
        raise ValueError("rewards and values must have the same length")
    ...
```

## 各區塊說明

| 區塊 | 必填？ | 說明 |
|---|:---:|---|
| 第一行摘要 | ✅ | 一句話說明函式做什麼，動詞開頭，句號結尾 |
| `Args:` | ✅（有參數時） | 每個參數名稱、型別、說明 |
| `Returns:` | ✅（非 None 時） | 回傳值的型別與說明 |
| `Raises:` | 視情況 | 可能拋出的例外與觸發條件 |
| `Example:` | 選填 | 使用範例，對複雜函式很有幫助 |

## 類別範例

```python
class ReplayBuffer:
    """Fixed-size experience replay buffer for off-policy RL.

    Stores (obs, action, reward, next_obs, done) transitions and
    supports uniform random sampling.

    Attributes:
        capacity: Maximum number of transitions to store.
        size: Current number of stored transitions.
    """

    def __init__(self, capacity: int) -> None:
        """Initialize an empty replay buffer.

        Args:
            capacity: Maximum number of transitions before old ones
                are overwritten.
        """
        self.capacity = capacity
        self.size = 0
```

## 最小規範（僅需這些）

實驗室要求「所有公開模組、類別與函式」都要有 docstring。對於簡單的私有輔助函式（名稱以 `_` 開頭），可以只寫一行摘要：

```python
def _normalize(x: np.ndarray) -> np.ndarray:
    """Normalize array to zero mean and unit variance."""
    return (x - x.mean()) / (x.std() + 1e-8)
```

---

**返回** [快速啟動](../getting_started/contributing.md) | **附錄首頁** [回到首頁](../index.md)
