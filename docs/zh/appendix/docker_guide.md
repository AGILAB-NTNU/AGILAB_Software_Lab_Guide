# Docker 容器化部署

`docker/` 資料夾用於封裝完整的執行環境，讓你的實驗可以在任意伺服器或雲端平台上精確重現，不受系統套件版本影響。

!!! info "何時需要 Docker？"
    一般開發期間用 conda 環境即可。以下情況才需要考慮 Docker：

    - 需要在多台伺服器之間遷移環境
    - 投稿時要求提供可執行的容器映像
    - 部署推論服務（非訓練）

---

## 基本 Dockerfile

```dockerfile
# docker/Dockerfile
FROM pytorch/pytorch:2.2.1-cuda12.1-cudnn8-runtime

WORKDIR /workspace

# 安裝系統依賴
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

# 安裝 Python 依賴
COPY pyproject.toml .
RUN pip install -e .[dev]

# 複製專案程式碼
COPY src/ src/
COPY configs/ configs/
```

---

## 搭配 GPU 的 docker-compose.yml

```yaml
# docker/docker-compose.yml
services:
  train:
    build:
      context: ..
      dockerfile: docker/Dockerfile
    volumes:
      - ../data:/workspace/data      # 掛載資料（不打包進映像）
      - ../logs:/workspace/logs
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
```

---

## 常用指令

```bash
# 建立映像
docker compose -f docker/docker-compose.yml build

# 啟動訓練容器
docker compose -f docker/docker-compose.yml run train \
    python -m your_project.training.train

# 進入容器互動模式（除錯用）
docker compose -f docker/docker-compose.yml run train bash
```

---

## 注意事項

- `data/` 資料夾透過 volume 掛載，不打包進映像，保持映像輕量。
- 基底映像（`pytorch/pytorch:...`）版本應與 `environment.yml` 中的 PyTorch 版本一致。
- `.gitignore` 中已排除 `docker/` 產生的暫存檔（`*.tar`、`__pycache__`）。

## Docker 伺服器部署進階實戰

### 1. 確保主機已配置 NVIDIA Container Toolkit
要在 Docker 容器中使用 GPU 加速（即在 `docker-compose.yml` 中指定 `driver: nvidia`），主機必須安裝並配置好 **NVIDIA Container Toolkit**。
驗證主機是否配置成功，請執行：
```bash
docker run --rm --gpus all nvidia/cuda:12.1.0-base-ubuntu22.04 nvidia-smi
```
如果能正確輸出 `nvidia-smi` 的 GPU 資訊，則代表配置正確。若出現 `unknown runtime specified nvidia` 錯誤，請聯繫系統管理員安裝 toolkit。

### 2. 容器內非 Root 用戶權限處理（避免產生權限被鎖定之檔案）
預設情況下，Docker 容器內的程序會以 `root` 身分執行。當容器將 `logs/` 或 `data/` 掛載到主機磁碟時，產生的檔案擁有者會是 `root`，這會導致你在主機上無法直接編輯或刪除這些實驗日誌與權限鎖定的資料。

**解決方案：動態傳遞主機的 UID 與 GID**
修改你的 `docker-compose.yml`，在 service 下指定當前主機用戶：
```yaml
services:
  train:
    build:
      context: ..
      dockerfile: docker/Dockerfile
    user: "${UID}:${GID}"  # 使用主機當前用戶的 UID 與 GID 執行容器內程序
    environment:
      - HOME=/workspace    # 確保某些需要寫入用戶家目錄的套件能正常存取
```
並在啟動時執行：
```bash
# 啟動時動態傳入環境變數
UID=$(id -u) GID=$(id -g) docker compose -f docker/docker-compose.yml run train
```

---

**返回附錄總覽** [附錄總覽](index.md) | **回到手冊主頁** [回到首頁](../index.md)
