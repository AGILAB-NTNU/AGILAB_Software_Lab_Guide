# Logging 與除錯

---

## 為什麼不用 `print`？

`print` 是最直覺的除錯方式，但在研究程式碼中有幾個問題：

- 很難控制「哪些訊息要顯示、哪些不要」
- 發布程式碼前要逐一手動刪除
- 無法記錄到檔案
- 看不出訊息的嚴重程度

Python 的 `logging` 模組解決了以上所有問題。

---

## 基本設定

在 `src/your_project/utils/` 中建立一個統一的 logger 設定：

```python
# src/your_project/utils/logger.py
import logging
import sys


def get_logger(name: str) -> logging.Logger:
    """Get a configured logger for the given module name."""
    logger = logging.getLogger(name)

    if not logger.handlers:
        handler = logging.StreamHandler(sys.stdout)
        formatter = logging.Formatter(
            fmt="%(asctime)s | %(levelname)-8s | %(name)s | %(message)s",
            datefmt="%Y-%m-%d %H:%M:%S",
        )
        handler.setFormatter(formatter)
        logger.addHandler(handler)
        logger.setLevel(logging.INFO)

    return logger
```

在各模組中使用：

```python
# src/your_project/training/train.py
from your_project.utils.logger import get_logger

logger = get_logger(__name__)


def train(config):
    logger.info("Training started with seed %d", config.seed)
    for epoch in range(config.num_epochs):
        loss = run_epoch()
        logger.info("Epoch %d/%d | loss=%.4f", epoch + 1, config.num_epochs, loss)
    logger.info("Training complete.")
```

---

## Log 等級

由低到高：

| 等級 | 用途 | 範例 |
|---|---|---|
| `DEBUG` | 詳細的除錯資訊，開發時才開啟 | 每個 step 的 reward |
| `INFO` | 正常的程式進度 | epoch 結果、模型載入成功 |
| `WARNING` | 非預期但不致命的狀況 | checkpoint 不存在，從頭訓練 |
| `ERROR` | 嚴重錯誤，程式可能無法繼續 | 設定檔格式錯誤 |

```python
logger.debug("Step %d: reward=%.3f", step, reward)   # 細節
logger.info("Loaded checkpoint from %s", path)        # 正常流程
logger.warning("No checkpoint found, starting fresh") # 非預期
logger.error("Config file %s not found", config_path) # 錯誤
```

---

## 讀懂 Traceback（錯誤訊息）

遇到錯誤時，Python 會印出 **Traceback**，從下往上讀：

```
Traceback (most recent call last):
  File "scripts/train.py", line 12, in <module>
    train(config)
  File "src/your_project/training/train.py", line 45, in train
    loss = criterion(output, target)
  File "src/your_project/training/loss.py", line 23, in compute_loss
    return F.mse_loss(pred, target.float())
RuntimeError: Expected all tensors to be on the same device,
              but found at least two devices, cuda:0 and cpu!
```

**讀法：**

1. **最底行** = 真正的錯誤（`RuntimeError: ...`）
2. **往上找** = 哪一行程式碼觸發了這個錯誤
3. **最上面** = 程式從哪個入口點進來的

**這個例子的解法：** `pred` 在 GPU，`target` 在 CPU。找到 `target` 的來源，加上 `.to(device)`。

---

## 常見錯誤速查

### `RuntimeError: Expected all tensors to be on the same device`

```python
# ❌
loss = F.mse_loss(pred, target)  # pred 在 GPU，target 在 CPU

# ✅
loss = F.mse_loss(pred, target.to(pred.device))
```

### `RuntimeError: mat1 and mat2 shapes cannot be multiplied`

Tensor 形狀不符，通常是輸入維度搞錯：

```python
# 立刻印出形狀來確認
print(f"x shape: {x.shape}, weight shape: {weight.shape}")
```

### `CUDA out of memory`

GPU 記憶體不夠：

```python
# 縮小 batch size，或在不需要 gradient 的地方加 torch.no_grad()
with torch.no_grad():
    output = model(obs)
```

### `ModuleNotFoundError: No module named 'your_project'`

套件沒有以開發模式安裝，或 conda 環境沒啟動：

```bash
conda activate agilab_env
pip install -e .
```

---

## 使用 `breakpoint()` 中斷除錯

在程式碼中插入 `breakpoint()`，執行到該行時會進入互動模式：

```python
def compute_loss(pred, target):
    breakpoint()  # 在這裡暫停，讓你檢查變數
    return F.mse_loss(pred, target)
```

進入互動模式後：

```
(Pdb) pred.shape          # 查看 tensor 形狀
(Pdb) target.device       # 查看裝置
(Pdb) n                   # 執行下一行
(Pdb) c                   # 繼續執行到下一個 breakpoint
(Pdb) q                   # 退出
```

!!! warning
    記得在 commit 前移除所有 `breakpoint()`。

---

**下一步 →** [實驗管理](experiment_management.md)
