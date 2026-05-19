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

---

**附錄首頁** [回到首頁](../index.md)
