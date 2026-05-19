# 進階測試技巧

本頁是 [撰寫與執行測試](../development/testing.md) 的進階補充，適合已經熟悉基本 pytest 用法的學生。

---

## pytest 常用指令

```bash
# 執行所有測試
pytest tests/

# 執行特定檔案
pytest tests/test_reward.py

# 執行特定函式
pytest tests/test_reward.py::test_reward_is_zero_when_distance_is_zero

# 顯示詳細輸出（看每個測試的名稱）
pytest tests/ -v

# 第一個測試失敗時立刻停止（快速定位問題）
pytest tests/ -x
```

---

## 測試預期的例外（`pytest.raises`）

有時候你希望某段程式碼**必須拋出例外**，可以用 `pytest.raises` 驗證：

```python
import pytest
from your_project.utils.reproducibility import seed_everything


def test_invalid_seed_raises():
    with pytest.raises(TypeError):
        seed_everything("not_an_int")  # 傳入字串應該要報錯
```

---

## Fixture：共用測試前置準備

當多個測試需要相同的初始化（例如都需要建立一個 buffer），可以用 `@pytest.fixture` 避免重複：

```python
import pytest
import numpy as np
from your_project.utils.replay_buffer import ReplayBuffer


@pytest.fixture
def buffer():
    """建立一個容量為 100 的 buffer，供多個測試共用。"""
    return ReplayBuffer(capacity=100)


def test_buffer_stores_transitions(buffer):
    obs = np.zeros(4)
    buffer.add(obs, np.array([0.5]), reward=1.0, next_obs=obs, done=False)
    assert len(buffer) == 1


def test_sample_returns_correct_batch_size(buffer):
    for _ in range(20):
        obs = np.random.randn(4)
        buffer.add(obs, np.array([0.0]), 0.0, obs, False)

    batch = buffer.sample(batch_size=8)
    assert len(batch["obs"]) == 8
```

`buffer` fixture 會在每個測試函式執行前自動呼叫，不需要在每個測試裡重複建立。

---

## 測試 Tensor 形狀

驗證模型輸入輸出的維度是否符合預期：

```python
import torch
from your_project.models.policy import PolicyNetwork


def test_policy_output_shape():
    model = PolicyNetwork(obs_dim=10, action_dim=4)
    obs = torch.zeros(8, 10)   # batch_size=8, obs_dim=10
    output = model(obs)
    assert output.shape == (8, 4)
```

---

## 完整測試範例：ReplayBuffer

```python
# tests/test_replay_buffer.py
import pytest
import numpy as np
from your_project.utils.replay_buffer import ReplayBuffer


@pytest.fixture
def buffer():
    return ReplayBuffer(capacity=100)


def test_buffer_stores_transitions(buffer):
    obs = np.zeros(4)
    action = np.array([0.5])
    buffer.add(obs, action, reward=1.0, next_obs=obs, done=False)
    assert len(buffer) == 1


def test_buffer_respects_capacity():
    small_buffer = ReplayBuffer(capacity=5)
    for i in range(10):
        obs = np.zeros(4)
        small_buffer.add(obs, np.array([0.0]), float(i), obs, False)
    assert len(small_buffer) == 5  # 超過容量時，舊資料被覆蓋


def test_sample_returns_correct_batch_size(buffer):
    for _ in range(20):
        obs = np.random.randn(4)
        buffer.add(obs, np.array([0.0]), 0.0, obs, False)

    batch = buffer.sample(batch_size=8)
    assert len(batch["obs"]) == 8
```
