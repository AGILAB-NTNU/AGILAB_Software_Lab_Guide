# 撰寫與執行測試

!!! info "測試是什麼？"
    你寫一小段程式，它會**自動驗證你的函式輸出是否正確**。每次修改程式後跑一次，立刻知道有沒有搞壞東西。

---

## 為什麼要寫測試？

研究程式碼很容易發生「改了 A，不知不覺壞了 B」的情況。測試讓這類問題在 commit 之前就被抓到，而不是訓練到一半才發現。

---

## 第一步：新增一個測試檔案

測試檔案放在 `tests/` 目錄，**檔名和函式名都以 `test_` 開頭**，pytest 才能自動找到它們：

```
tests/
└── test_reward.py    ← 你的測試放這裡
```

---

## 第二步：寫一個最簡單的測試

假設你有一個計算獎勵的函式：

```python
# src/your_project/reward.py
def compute_reward(distance: float) -> float:
    """距離越小，獎勵越高。"""
    return -distance
```

對應的測試這樣寫：

```python
# tests/test_reward.py
from your_project.reward import compute_reward


def test_reward_is_negative_for_positive_distance():
    reward = compute_reward(distance=1.0)
    assert reward == -1.0


def test_reward_is_zero_when_distance_is_zero():
    reward = compute_reward(distance=0.0)
    assert reward == 0.0
```

`assert` 的意思是「確認這個條件成立，如果不成立就讓測試失敗」。

---

## 第三步：執行測試

```bash
# 執行所有測試
pytest tests/

# 執行特定檔案
pytest tests/test_reward.py
```

看到 `passed` 就代表通過，看到 `FAILED` 代表有問題需要修正。

---

## 什麼時候要寫測試？

不需要測試每一行程式碼，優先測試這兩類：

| 優先度 | 對象 | 原因 |
|:---:|---|---|
| ★★★ | 獎勵函式、損失計算 | 數值錯誤很難從訓練曲線發現 |
| ★★★ | 資料前處理 | 形狀或值域錯誤會悄悄傳播 |
| ★★☆ | 模型輸入輸出的 Tensor shape | 早期發現 shape 不匹配 |

---

## 在 Commit 前跑測試

養成習慣：每次 commit 前先確認測試都過：

```bash
pytest tests/
```

---

!!! info "想學更多？"
    → [進階測試技巧](../appendix/testing_advanced.md)：更多 pytest 指令、fixture、pytest.raises、完整 ReplayBuffer 測試範例

**下一步 →** [Logging 與除錯](logging_debugging.md)
