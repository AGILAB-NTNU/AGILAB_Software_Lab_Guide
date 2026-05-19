# Logging 與除錯

---

## 用 logging 取代 print

`print` 很直覺，但有個問題：當程式碼越來越大，你會不知道哪些 `print` 是重要訊息、哪些是臨時除錯用的，最後要整理時得一個個手動刪。

Python 內建的 `logging` 模組可以解決這個問題，而且用法幾乎一樣簡單：

```python
import logging

logging.basicConfig(level=logging.INFO, format="%(levelname)s | %(message)s")
logger = logging.getLogger(__name__)

# 使用方式就像 print
logger.info("Training started")
logger.info("Epoch %d | loss=%.4f", epoch, loss)
```

輸出：

```
INFO | Training started
INFO | Epoch 1 | loss=0.3241
```

把這兩行放在每個檔案的最上方，就可以開始用了：

```python
import logging
logger = logging.getLogger(__name__)
```

---

## Log 等級

logging 有四個常用等級，讓你區分訊息的重要程度：

| 用法 | 適合記錄什麼 |
|---|---|
| `logger.debug(...)` | 細節資訊（預設不顯示，除錯時才開啟） |
| `logger.info(...)` | 正常流程，例如 epoch 進度、模型載入成功 |
| `logger.warning(...)` | 非預期但不致命的狀況，例如找不到 checkpoint |
| `logger.error(...)` | 嚴重錯誤 |

一般訓練時用 `info` 記錄進度就夠了。

---

## 讀懂錯誤訊息（Traceback）

程式報錯時，Python 會印出一段 **Traceback**。很多人看到這大段文字會緊張，但其實只需要看**最下面一行**：

```
Traceback (most recent call last):
  File "scripts/train.py", line 12, in <module>
    train(config)
  File "src/your_project/training/train.py", line 45, in train
    loss = criterion(output, target)
RuntimeError: Expected all tensors to be on the same device,
              but found at least two devices, cuda:0 and cpu!
```

**讀法：從最下面往上看**

1. **最底行** → 真正的錯誤是什麼（`RuntimeError: ...`）
2. **往上一層** → 哪一行程式碼造成的（`loss = criterion(output, target)`）

這個例子的意思是：`output` 在 GPU，`target` 在 CPU，兩者裝置不同所以出錯。

---

## 常見錯誤速查

### `ModuleNotFoundError: No module named 'your_project'`

套件沒有安裝，或 conda 環境沒啟動：

```bash
conda activate agilab_env
pip install -e .
```

### `RuntimeError: Expected all tensors to be on the same device`

Tensor 在不同裝置（CPU / GPU），把它們統一：

```python
loss = F.mse_loss(pred, target.to(pred.device))
```

### `CUDA out of memory`

GPU 記憶體不夠，先縮小 batch size，或在推論時加 `torch.no_grad()`：

```python
with torch.no_grad():
    output = model(obs)
```

---

!!! info "想學更多？"
    → [進階 Logging 與除錯](logging_advanced.md)：統一 logger 模組、寫入 log 檔案、breakpoint 互動除錯

**下一步 →** [實驗管理](experiment_management.md)
