# 實驗管理

研究的核心是「比較不同設定下的結果」。沒有好的實驗管理，很容易發生「這個好結果是哪次跑的？用什麼參數？」的情況。

---

## 核心原則

!!! important "提交程式碼後再跑實驗"
    **每次正式實驗前，先 git commit 你的程式碼**。這讓你能用 Git Hash 精確定位「這個結果是哪版程式碼產生的」。

    ```bash
    # 確認目前的 commit hash
    git log --oneline -1
    # 例如：a1b2c3d feat: Add attention to policy
    ```

---

## 1. 用設定檔管理超參數

**不要在程式碼裡硬編碼超參數**，改用 `configs/` 目錄的 YAML 檔：

```yaml
# configs/ppo_humanoid.yaml
seed: 42
env:
  name: "HumanoidStand-v1"
  max_steps: 1000

agent:
  lr: 3e-4
  gamma: 0.99
  clip_ratio: 0.2
  batch_size: 64

logging:
  save_dir: "data/checkpoints"
  log_interval: 100
```

每次實驗換參數時，複製一份設定檔修改，而非直接改程式碼：

```bash
cp configs/ppo_humanoid.yaml configs/ppo_humanoid_lr1e4.yaml
# 修改 lr: 1e-4
```

---

## 2. Checkpoint 命名規範

儲存模型權重時，用**能識別實驗身份的名稱**：

```
[專案名]_[日期]_[關鍵指標].[副檔名]
```

實際範例：

```
humanoid_20240501_reward312.pt
humanoid_20240503_reward298.pt
```

在程式碼中自動生成：

```python
import time
from pathlib import Path


def save_checkpoint(
    model: torch.nn.Module,
    reward: float,
    save_dir: str,
    project_name: str,
) -> Path:
    """Save model checkpoint with timestamped filename."""
    save_dir = Path(save_dir)
    save_dir.mkdir(parents=True, exist_ok=True)

    date_str = time.strftime("%Y%m%d")
    filename = f"{project_name}_{date_str}_reward{reward:.0f}.pt"
    path = save_dir / filename

    torch.save(model.state_dict(), path)
    return path
```

---

## 3. 在訓練時記錄關鍵資訊

每次實驗結束後，你應該能回答這些問題：

| 問題 | 記錄方式 |
|---|---|
| 用了哪個 commit 的程式碼？ | logger 印出 `git rev-parse HEAD` |
| 用了什麼超參數？ | logger 印出完整 config |
| 最好的結果在第幾個 epoch？ | 追蹤 best_reward |
| Checkpoint 存在哪裡？ | logger 印出完整路徑 |

```python
import subprocess
from your_project.utils.logger import get_logger

logger = get_logger(__name__)


def log_experiment_info(config: dict) -> None:
    """Log experiment metadata at the start of training."""
    git_hash = subprocess.check_output(
        ["git", "rev-parse", "--short", "HEAD"]
    ).decode().strip()

    logger.info("=" * 50)
    logger.info("Git commit: %s", git_hash)
    logger.info("Config: %s", config)
    logger.info("=" * 50)
```

---

## 4. 整理實驗結果

建議在專案的 `docs/experiment_log.md` 中維護一個實驗結果表，不要直接寫在 `README.md`，保持 README 簡潔：

```markdown
# 實驗記錄

| 日期 | Commit | 設定檔 | 最高 Reward | 備註 |
|---|---|---|---|---|
| 2024-05-01 | `a1b2c3d` | ppo_humanoid.yaml | 312 | baseline |
| 2024-05-03 | `e4f5g6h` | ppo_humanoid_lr1e4.yaml | 298 | lr 太小 |
| 2024-05-05 | `i7j8k9l` | ppo_attention.yaml | 341 | attention ✅ |
```

在 `README.md` 中只需加一行連結即可：

```markdown
實驗記錄請見 [docs/experiment_log.md](docs/experiment_log.md)。
```

---

## 5. 伺服器執行時的注意事項

在伺服器上執行長時間訓練時，建議使用 **tmux** 管理 session，讓訓練在登出後繼續執行。

```bash
# 建立一個新的 session（取個好記的名字）
tmux new -s train

# 在 session 中啟動訓練
python -m your_project.training.train --config configs/ppo_humanoid.yaml

# 需要離開時：按下 Ctrl+B，放開，再按 D
# 程式會繼續在背景跑

# 之後重新連回 session
tmux attach -t train

# 列出目前所有 session
tmux ls
```

!!! tip "還沒安裝 tmux？"
    伺服器通常沒有 sudo 權限，請參考 → [tmux 無 sudo 安裝教學](../appendix/tmux_guide.md)

---

**下一步 →** [研究可重現性](../research/reproducibility.md)
