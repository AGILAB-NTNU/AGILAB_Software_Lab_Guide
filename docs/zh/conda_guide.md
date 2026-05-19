# 環境管理指南 (Conda & Environment Guide)

在研究中，環境配置（尤其是涉及 CUDA 與實體模擬器時）往往是最耗時的步驟。本指南定義了實驗室統一的環境管理流程。

## 1. 核心原則

*   **唯一來源**：所有的依賴必須記錄在 `environment.yml` 中。嚴禁僅透過 `pip install` 安裝而不更新設定檔。
*   **導出環境**：當您成功配置了一個複雜環境時，請使用以下指令備份：
    ```bash
    conda env export --no-builds > environment.yml
    ```

## 2. 處理 CUDA 依賴

為了確保 GPU 加速能正常運作，我們建議在 `environment.yml` 中明確指定 `pytorch-cuda` 版本：

```yaml
dependencies:
  - python=3.9
  - pytorch
  - torchvision
  - pytorch-cuda=12.1  # 明確鎖定 CUDA 版本
  - conda-forge::cupy  # 若需要 Cupy
```

## 3. 硬體與驅動規範 (Hardware & Driver)

由於 CUDA 與實體硬體有強耦合關係，請務必在專案的 `README.md` 中註明以下硬體基線：

*   **GPU 型號**：說明實驗使用的顯示卡（如 `NVIDIA GeForce RTX 4090`）。不同架構（如 Turing vs. Ada Lovelace）可能會影響算子的執行。
*   **驅動版本 (Driver Version)**：執行 `nvidia-smi` 查看到的驅動版本（如 `535.129.03`）。這決定了機器最高能支援的 CUDA 版本。
*   **指令集與計算能力 (Compute Capability)**：若使用了編譯型的自定義算子，請註明對應的計算能力（如 `sm_89`）。

!!! warning "「程式碼能跑」不代表「結果一致」"
    若發現實驗結果在不同顯卡上有顯著差異，請檢查不同顯卡的 `Float32` 運算精度與 `Tensor Cores` 的調用情況。

## 4. 疑難排解 (Troubleshooting)

### 發生解決依賴衝突 (Conflict) 時
如果 Conda 解決依賴過慢，請嘗試：
1.  **使用 Mamba**：`conda install mamba -n base -c conda-forge`。Mamba 是 C++ 實作的 Conda，速度快上數倍。
2.  **清理快取**：`conda clean --all`。

### 物理模擬器特殊安裝 (如 Isaac Gym / MuJoCo)
對於無法透過 Conda 直接安裝的模擬器：
1.  在專案根目錄建立 `third_party/` 資料夾。
2.  將模擬器源碼放入其中。
3.  在 `environment.yml` 的 `pip` 區塊中使用 `-e third_party/xxx` 進行安裝。

---

!!! tip "保持環境簡潔"
    如果您只是測試一個套件，請先建立一個臨時環境，確認需要後再加入專案的 `environment.yml`。

---

**下一步 →** [快速啟動與貢獻指南](contributing.md)
