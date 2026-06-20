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

當多個測試需要相同的初始化（例如都需要建立同一個資料集實例），可以用 `@pytest.fixture` 避免重複編寫初始化代碼：

```python
import pytest
import numpy as np
from your_project.data.dataset import SimpleDataset


@pytest.fixture
def dataset():
    """建立一個含有 100 筆隨機資料的資料集，供多個測試共用。"""
    features = np.random.randn(100, 8).astype(np.float32)
    labels = np.random.randint(0, 2, size=(100, 1)).astype(np.float32)
    return SimpleDataset(features, labels)


def test_dataset_length(dataset):
    assert len(dataset) == 100


def test_dataset_getitem(dataset):
    x, y = dataset[0]
    assert x.shape == (8,)
    assert y.shape == (1,)
```

`dataset` fixture 會在每個測試函式執行前自動被呼叫並傳入，不需要在每個測試裡重複編寫初始化邏輯。

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

## 完整測試範例：Dataset

在 AI/深度學習研究中，資料集（Dataset）與資料管道的測試需要模擬實際神經網路更新時所需要的 Batch 資料結構與型別。以下是學術界通用的測試寫法：

```python
# tests/test_dataset.py
import pytest
import numpy as np
from your_project.data.dataset import SimpleDataset


@pytest.fixture
def dataset():
    """建立一個供測試使用的 Dataset 實例。"""
    features = np.random.randn(100, 8).astype(np.float32)
    labels = np.random.randint(0, 2, size=(100, 1)).astype(np.float32)
    return SimpleDataset(features, labels)


def test_dataset_initialization(dataset):
    # 驗證資料集長度是否正確
    assert len(dataset) == 100


def test_dataset_getitem_returns_correct_shape(dataset):
    # 驗證單筆讀取的特徵與標籤維度
    x, y = dataset[0]
    assert x.shape == (8,)
    assert y.shape == (1,)
    assert x.dtype == np.float32


def test_batch_sampling_shapes_and_types(dataset):
    # 採樣 batch_size=16
    x_batch, y_batch = dataset.get_batch(batch_size=16)

    # 驗證採樣後的 NumPy Array 維度與型別是否符合 AI 網路輸入的常規要求
    assert x_batch.shape == (16, 8)
    assert y_batch.shape == (16, 1)
    
    assert x_batch.dtype == np.float32
    assert y_batch.dtype == np.float32
```


## Mocking：模擬外部相依性與硬體 API

在機器人與增強式學習研究中，測試經常需要與外部硬體（例如實體機械手臂、感測器）或複雜的模擬器互動。我們不希望單元測試因為需要連接硬體而卡死，也不希望每次測試都真的去啟動龐大的模擬器（例如 Isaac Gym）。

這時，我們可以使用以下兩個工具來進行「隔離模擬」：

1. **`MagicMock`（來自 Python 內建的 `unittest.mock`）**：
    * **定義**：它是一個「仿製品」或稱「替身」物件。你可以用它替換掉真實的函式或類別，並手動指定它的回傳值。
    * **用途**：可以用來模擬硬體控制器的回傳（例如直接回傳「手臂已歸零」或「連線成功」），讓你的主程式可以繼續往下跑而不需要真實連線。
2. **`monkeypatch`（pytest 內建的 Fixture）**：
    * **定義**：它是一個能在測試執行的「短暫期間內」，安全地修改、新增或替換環境變數、模組屬性或全域設定的工具。
    * **用途**：測試結束後它會自動恢復原狀，確保測試過程中不會對系統的環境變數造成污染，常用於模擬 WandB 金鑰或伺服器 IP 配置。

### 1. 使用 MagicMock 模擬實體機械手臂控制
假設你有一個 `RobotController` 會發送網路指令給實體手臂：
```python
# your_project/utils/robot.py
class RobotController:
    def move_to(self, position: list[float]) -> bool:
        # 實際上會建立 TCP 連線並等待實體手臂移動，這很慢且依賴實體設備
        print("Connecting to robot arm...")
        return True
```
我們可以在測試中使用 `MagicMock` 來模擬這個呼叫，使其立即回傳 `True`：
```python
from unittest.mock import MagicMock
from your_project.utils.robot import RobotController

def test_robot_movement_flow():
    # 建立控制器實例
    controller = RobotController()
    
    # 用 MagicMock 替換真實的 move_to 方法
    controller.move_to = MagicMock(return_value=True)
    
    # 執行依賴此方法的邏輯
    success = controller.move_to([0.5, 0.2, 0.1])
    
    # 驗證方法是否被正確呼叫、傳入正確的參數，且回傳預期結果
    assert success is True
    controller.move_to.assert_called_once_with([0.5, 0.2, 0.1])
```

### 2. 使用 `monkeypatch` 模擬環境變數或組態
有時程式碼會去讀取外部的環境變數（例如 WandB API Key），我們可以用 `monkeypatch` 在測試執行期間將其替換：
```python
def test_wandb_config(monkeypatch):
    # 在測試執行期間，強制將環境變數 WANDB_API_KEY 設定為 mock_key
    monkeypatch.setenv("WANDB_API_KEY", "mock_key_12345")
    
    # 這裡執行會讀取環境變數的程式碼
    import os
    assert os.getenv("WANDB_API_KEY") == "mock_key_12345"
```

---

**返回** [撰寫與執行測試](../development/testing.md) | **返回附錄總覽** [附錄總覽](index.md) | **回到手冊主頁** [回到首頁](../index.md)
