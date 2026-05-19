# 撰寫與執行測試

!!! info "對新手的說明"
    「測試」在這裡指的是**自動化測試**：你寫一段程式碼，它會自動驗證你的程式邏輯是否正確。每次修改程式後跑一次測試，就能立刻知道有沒有搞壞什麼東西。

---

## 為什麼要寫測試？

研究程式碼的一個常見問題是：改了 A 功能，結果不知不覺破壞了 B 功能。有了測試，這類問題會立刻被抓到。

實驗室的 CI（持續整合）在每次 PR 時會自動執行所有測試。測試沒過 = PR 不能合進 `dev`。

---

## 基本結構

測試檔案放在 `tests/` 目錄，檔名以 `test_` 開頭：

```
tests/
├── test_models.py
├── test_utils.py
└── test_reward.py
```

每個測試函式也以 `test_` 開頭，pytest 會自動找到它們：

```python
# tests/test_utils.py
import pytest
from your_project.utils.reproducibility import seed_everything


def test_seed_everything_sets_torch_seed():
    """Seeding should produce identical results across runs."""
    import torch

    seed_everything(42)
    a = torch.rand(3)

    seed_everything(42)
    b = torch.rand(3)

    assert torch.allclose(a, b)
```

---

## 執行測試

```bash
# 執行所有測試
pytest tests/

# 執行特定檔案
pytest tests/test_utils.py

# 執行特定函式
pytest tests/test_utils.py::test_seed_everything_sets_torch_seed

# 顯示詳細輸出
pytest tests/ -v

# 測試失敗時立刻停止
pytest tests/ -x
```

---

## 斷言（Assert）

測試的核心是 `assert`：驗證某個條件為真，若為假則測試失敗。

```python
def test_compute_reward_positive():
    reward = compute_reward(obs=1.0, scale=2.0)
    assert reward == 2.0                    # 值相等
    assert reward > 0                       # 比較
    assert isinstance(reward, float)        # 型別檢查

def test_output_shape():
    import torch
    model = PolicyNetwork(obs_dim=10, action_dim=4)
    obs = torch.zeros(8, 10)               # batch_size=8
    output = model(obs)
    assert output.shape == (8, 4)          # 確認輸出形狀
```

---

## 測試預期的例外

有時候你希望某段程式碼**必須拋出例外**，用 `pytest.raises`：

```python
def test_invalid_seed_raises():
    with pytest.raises(TypeError):
        seed_everything("not_an_int")  # 應該要報錯
```

---

## 什麼時候要寫測試？

不需要測試每一行程式碼。優先測試這些：

| 優先度 | 對象 | 原因 |
|:---:|---|---|
| ★★★ | 獎勵函式、損失計算 | 數值錯誤難以從訓練曲線察覺 |
| ★★★ | 資料前處理 | 形狀/值域錯誤會靜默傳播 |
| ★★☆ | 模型輸入輸出形狀 | 早期發現 Tensor shape 不匹配 |
| ★★☆ | 工具函式（utils） | 被大量呼叫，出錯影響大 |
| ★☆☆ | 訓練迴圈本體 | 通常依賴 GPU，整合測試較合適 |

---

## 一個完整的測試範例

```python
# tests/test_replay_buffer.py
import pytest
import numpy as np
from your_project.utils.replay_buffer import ReplayBuffer


@pytest.fixture
def buffer():
    """建立一個容量為 100 的 buffer，供測試使用。"""
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
    assert len(small_buffer) == 5  # 超過容量時舊資料被覆蓋


def test_sample_returns_correct_batch_size(buffer):
    for _ in range(20):
        obs = np.random.randn(4)
        buffer.add(obs, np.array([0.0]), 0.0, obs, False)

    batch = buffer.sample(batch_size=8)
    assert len(batch["obs"]) == 8
```

---

## 在 Commit 前跑測試

養成習慣：每次 commit 前先確認測試都過：

```bash
pytest tests/ && git add ... && git commit -m "..."
```

---

**下一步 →** [Logging 與除錯](logging_debugging.md)
