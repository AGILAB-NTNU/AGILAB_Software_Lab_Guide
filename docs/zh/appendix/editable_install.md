# 開發模式安裝（Editable Install）

!!! info "這是什麼？"
    `pip install -e .` 中的 `-e` 代表 **editable（可編輯）模式**。這讓你的套件以「連結」的方式安裝，而非複製。

## 一般安裝 vs 開發模式安裝

### 一般安裝

```bash
pip install .
```

Python 會把你的程式碼**複製**到 `site-packages/` 目錄。這表示你每次修改程式碼後，都必須重新執行 `pip install .` 才能讓變更生效。

### 開發模式安裝

```bash
pip install -e .
```

Python 只是在 `site-packages/` 建立一個**捷徑（symlink）**指向你的原始碼。這表示你**直接修改 `src/` 裡的程式碼就會立即生效**，不需要重新安裝。

## 為什麼研究專案要用開發模式？

研究過程中，你會頻繁修改模型架構、訓練迴圈等核心程式碼。如果每次修改都要重新安裝，不僅麻煩，也容易忘記，導致「明明改了程式碼，但跑的還是舊版」的 bug。

## 如何驗證已正確安裝

```bash
# 將 your_project 替換成你的專案名稱
python -c "import your_project; print('安裝成功！')"
```

若沒有報錯，代表安裝正常。若出現 `ModuleNotFoundError`，請確認：

1. 你已啟動正確的 conda 環境：`conda activate agilab_env`
2. 你在專案根目錄（`pyproject.toml` 所在目錄）執行過 `pip install -e .`

## 開發模式下的特別注意事項

### 1. 修改 pyproject.toml 中的 Metadata 需重新安裝
在開發模式下，雖然修改 `.py` 檔案內容會立即生效，但如果你修改了 `pyproject.toml` 中的關鍵定義，例如：
- 專案名稱 `name`
- 套件入口指令 `[project.scripts]` (entry points)
- 新增相依套件列表

Python **不會自動動態追蹤這些設定的變更**。你必須手動重新執行以下指令以更新本地捷徑與環境 metadata：
```bash
pip install -e .
```

### 2. 物理模擬器與第三方專案（Third-Party）的開發模式
若你的專案引用了實驗室其他自研的第三方工具（放在 `third_party/`），你也可以使用 `-e` 對其進行開發模式安裝，這樣當你在調校這些庫的原始碼時，主專案就能立即取得變更：
```bash
pip install -e third_party/your_custom_simulator
```

### 3. 如何解除安裝？
當你需要清除本地的捷徑連結並徹底卸載此專案套件時，可以使用：
```bash
pip uninstall your_project_name
```

---

**返回** [快速啟動](../getting_started/contributing.md) | **返回附錄總覽** [附錄總覽](index.md) | **回到手冊主頁** [回到首頁](../index.md)
