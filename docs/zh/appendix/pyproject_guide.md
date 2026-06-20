# pyproject.toml 說明

`pyproject.toml` 是 Python 標準的套件設定檔，定義「這個專案叫什麼、如何安裝、程式碼風格是什麼」。

---

## 與 environment.yml 的分工

這兩個檔案都與套件有關，但職責不同：

| | `environment.yml` | `pyproject.toml` |
|---|---|---|
| 管理工具 | conda | pip |
| 主要用途 | 建立與重現執行環境 | 讓專案可被 import 與發布 |
| 何時修改 | 每次新增套件 | 初始化時改一次名稱即可 |
| 新手需要管嗎 | ✅ 是 | 僅改名稱 |

**日常維護套件時，只需要更新 `environment.yml`**，不需要手動同步 `pyproject.toml` 的依賴列表。

→ 詳見 [Conda 與 environment.yml 說明](conda_env_guide.md)

---

## 初始化：需要修改的部分

Clone 專案後，只需改這兩行：

```toml
[project]
name = "project_name"       # ← 改成你的專案名稱（小寫、底線分隔）
description = "..."         # ← 一句話說明這個專案做什麼
version = "0.1.0"
```

同時將 `src/project_name/` 目錄重命名，讓名稱與這裡一致。改完後執行：

```bash
pip install -e .[dev]
```

→ 詳見 [開發模式安裝](editable_install.md)

---

## Ruff 程式碼風格設定

```toml
[tool.ruff]
line-length = 88
target-version = "py39"

[tool.ruff.lint]
select = ["E", "F", "W", "I", "N"]
```

| 規則集 | 說明 |
|---|---|
| `E` / `W` | PEP 8 風格（縮排、空白） |
| `F` | Pyflakes（未使用變數、未引入模組） |
| `I` | isort（import 排序） |
| `N` | 命名規範（類別用 PascalCase 等） |

這些規則在 commit 時由 pre-commit 自動檢查，不需要手動執行。

---

## 將專案發布為套件

研究結束後，若想讓其他人能用 `pip install` 安裝你的專案，需要以下步驟。

### 1. 確認版本號

```toml
[project]
version = "1.0.0"   # 遵循 major.minor.patch 語義化版本
```

### 2. 建立發布用的壓縮包

```bash
pip install build
python -m build
```

執行後會在 `dist/` 產生兩個檔案：
```
dist/
├── your_project-1.0.0.tar.gz      # 原始碼包
└── your_project-1.0.0-py3-none-any.whl  # 預編譯包
```

### 3a. 發布到 PyPI（公開）

```bash
pip install twine
twine upload dist/*
```

上傳後任何人都可以 `pip install your_project` 安裝。需要先在 [PyPI](https://pypi.org) 註冊帳號。

### 3b. 透過 GitHub Release（研究常用）

不想上 PyPI 的話，可以直接在 GitHub 建立 Release，並將 `dist/` 中的 `.whl` 檔附上。其他人可以直接用 URL 安裝：

```bash
pip install https://github.com/AGILAB-NTNU/your_project/releases/download/v1.0.0/your_project-1.0.0-py3-none-any.whl
```

!!! tip
    論文 reproducibility 常見作法：在 README 提供 GitHub Release 的 pip 安裝指令，讓讀者一行指令就能安裝對應版本的程式碼。

---

**返回** [環境管理](../getting_started/conda_guide.md) | **返回附錄總覽** [附錄總覽](index.md) | **回到手冊主頁** [回到首頁](../index.md)

