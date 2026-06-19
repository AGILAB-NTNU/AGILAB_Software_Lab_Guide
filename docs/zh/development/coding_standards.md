# 程式碼規範

實驗室的程式碼規範目標只有一個：**讓隊友（和未來的你）能快速讀懂你寫的東西**。

本頁說明三個需要遵守的規範，每個都有附錄提供詳細範例。

---

## 1. Google Python Style Guide

我們遵守 Google Python Style Guide 作為整體程式碼風格的基準，包含命名方式、縮排、import 順序等。

實際執行時不需要手動記，**Ruff 會自動檢查並修正大部分問題**：

```bash
ruff check . --fix   # 檢查並自動修正
ruff format .        # 統一格式
```

commit 前跑一次這兩個指令就夠了。

→ [Google Python Style Guide 重點整理](../appendix/google_style_guide.md)

也可一開始在terminal輸入以下指令，之後提交會自動修正
```python
 pre-commit install 

```

---

## 2. Type Hinting

所有函式簽名都必須加型別提示：

```python
# 不好
def compute_reward(distance):
    return -distance

# 好
def compute_reward(distance: float) -> float:
    return -distance
```

型別提示讓隊友在呼叫你的函式時立刻知道要傳什麼、會回傳什麼，不需要去讀實作細節。

→ [Type Hinting 入門](../appendix/type_hinting.md)

---

## 3. Google Style Docstrings

所有公開的模組、類別、函式都需要寫 docstring：

```python
def compute_reward(distance: float) -> float:
    """根據距離計算獎勵值。

    Args:
        distance: 目標與當前位置的距離（公尺）。

    Returns:
        獎勵值，距離越小獎勵越高。
    """
    return -distance
```

私有的輔助函式（`_` 開頭）或一看就懂的單行函式可以省略。

→ [Google Style Docstrings 範例](../appendix/docstrings.md)

---

**下一步 →** [日常 Git 操作](daily_git.md)
