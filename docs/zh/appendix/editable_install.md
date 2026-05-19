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

---

**返回** [快速啟動](../getting_started/contributing.md) | **附錄首頁** [回到首頁](../index.md)
