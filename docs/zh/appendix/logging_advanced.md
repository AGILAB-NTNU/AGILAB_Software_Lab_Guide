# 進階 Logging 與除錯

本頁是 [Logging 與除錯](../development/logging_debugging.md) 的進階補充，適合想要更完整控制 log 輸出的學生。

---

## 建立統一的 Logger 模組

當專案變大，每個檔案都寫 `basicConfig` 會造成重複設定的問題。建議在 `utils/` 中建立一個共用的 logger 工廠：

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
```

輸出格式：

```
2024-05-01 14:32:01 | INFO     | your_project.training.train | Training started with seed 42
2024-05-01 14:32:05 | INFO     | your_project.training.train | Epoch 1/100 | loss=0.3241
```

---

## 將 Log 同時寫入檔案

在伺服器上跑長時間訓練時，把 log 存成檔案方便事後查閱：

```python
def get_logger(name: str, log_file: str | None = None) -> logging.Logger:
    """Get a configured logger, optionally writing to a file."""
    logger = logging.getLogger(name)

    if not logger.handlers:
        formatter = logging.Formatter(
            fmt="%(asctime)s | %(levelname)-8s | %(name)s | %(message)s",
            datefmt="%Y-%m-%d %H:%M:%S",
        )

        # 輸出到終端機
        stream_handler = logging.StreamHandler(sys.stdout)
        stream_handler.setFormatter(formatter)
        logger.addHandler(stream_handler)

        # 同時寫入檔案
        if log_file:
            file_handler = logging.FileHandler(log_file)
            file_handler.setFormatter(formatter)
            logger.addHandler(file_handler)

        logger.setLevel(logging.INFO)

    return logger
```

使用方式：

```python
logger = get_logger(__name__, log_file="logs/train_20240501.log")
```

---

## 使用 `breakpoint()` 中斷除錯

在程式碼中插入 `breakpoint()`，執行到該行時程式會暫停，讓你在終端機中直接檢查變數：

```python
def compute_loss(pred, target):
    breakpoint()  # 在這裡暫停
    return F.mse_loss(pred, target)
```

進入互動模式後：

```
(Pdb) pred.shape          # 查看 tensor 形狀
torch.Size([8, 4])
(Pdb) target.device       # 查看裝置
device(type='cpu')
(Pdb) n                   # 執行下一行
(Pdb) c                   # 繼續執行到下一個 breakpoint
(Pdb) q                   # 退出
```

!!! warning "記得移除"
    commit 前記得刪除所有 `breakpoint()`，否則程式在別人的環境執行時會意外暫停。

---

## Tensor shape 不符的除錯技巧

遇到 shape mismatch 時，最快的方式是直接印出各 tensor 的形狀：

```python
# RuntimeError: mat1 and mat2 shapes cannot be multiplied
print(f"x shape: {x.shape}")
print(f"weight shape: {weight.shape}")
```

確認後再修正維度順序或 reshape 操作。

## 常見踩坑：避免重複列印日誌（Duplicate Logs）

當你在多個模組中使用 `logger = get_logger(__name__)`，如果沒有正確處理 handler，或者多次呼叫了基礎設定，可能會發現終端機裡每一行 log 被印了兩次或多次。

### 1. 關閉 propagate 屬性
在 Python 的 logging 系統中，子 logger（例如 `your_project.models.policy`）預設會將日誌訊息向上傳遞（propagate）給 root logger。如果 root logger 也被配置了 handler，就會造成重複輸出。

解決方案是在自訂 Logger 中顯式關閉傳遞，或是只配置 root logger：
```python
def get_logger(name: str, log_file: str | None = None) -> logging.Logger:
    logger = logging.getLogger(name)
    
    # 關閉傳遞，防止子模組的日誌傳遞到 parent/root logger 重複印出
    logger.propagate = False
    ...
```

### 2. 日誌分級最佳實作（Console INFO + File DEBUG）
在伺服器上執行長跑實驗時，終端機通常只需顯示關鍵的訓練進度（`INFO`），而日誌檔案則需要記錄極其詳細的除錯資訊（`DEBUG`），以便在出錯時追查。

以下是進階雙通道日誌配置函式的完整範例：
```python
import os
import sys
import logging

def setup_logger(name: str, log_dir: str = "logs") -> logging.Logger:
    logger = logging.getLogger(name)
    logger.setLevel(logging.DEBUG)  # 設定最寬鬆的總門檻
    logger.propagate = False
    
    # 避免重複添加 handler
    if not logger.handlers:
        # 1. 建立日誌資料夾
        os.makedirs(log_dir, exist_ok=True)
        
        # 格式定義
        formatter = logging.Formatter(
            fmt="%(asctime)s | %(levelname)-8s | %(name)s | %(message)s",
            datefmt="%Y-%m-%d %H:%M:%S",
        )
        
        # 2. Console Handler (僅輸出 INFO 以上)
        console_handler = logging.StreamHandler(sys.stdout)
        console_handler.setLevel(logging.INFO)
        console_handler.setFormatter(formatter)
        logger.addHandler(console_handler)
        
        # 3. File Handler (輸出 DEBUG 以上，保留所有細節)
        file_path = os.path.join(log_dir, "experiment.log")
        file_handler = logging.FileHandler(file_path, encoding="utf-8")
        file_handler.setLevel(logging.DEBUG)
        file_handler.setFormatter(formatter)
        logger.addHandler(file_handler)
        
    return logger
```

---

**返回** [Logging 與除錯](../development/logging_debugging.md) | **返回附錄總覽** [附錄總覽](index.md) | **回到手冊主頁** [回到首頁](../index.md)
