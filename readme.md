#### # Airflow 主要組件
- Scheduler（排程器）➡️ 排程 & 觸發 DAG 任務
- Executor（執行器）➡️ 負責執行任務（本機、Celery、Kubernetes）
- Webserver（Web UI）➡️ 提供圖形化管理介面
- Metastore（資料庫） ➡️ 儲存 DAG 配置、任務狀態（通常使用 PostgreSQL / MySQL）

#### 目錄結構
airflow-docker/
│── dags/                # 放置 DAG 腳本
│── docker-compose.yml   # Docker Compose 設定檔


#### 步驟
- 在 dags/ 資料夾內新增 example_dag.py
- 初始化資料庫 
```bash
docker compose up airflow-db -d  # 先啟動資料庫
docker compose run airflow-webserver airflow db migrate  # 初始化資料庫
```

- 建立管理員帳號
```bash
docker compose run airflow-webserver airflow users create `
    -u admin `
    -p admin123 `
    -f Peter `
    -l Parker `
    -r Admin `
    -e spiderman@superhero.org
```

- 啟動 Airflow
```bash
docker compose up -d
    這將會啟動：
        - Web UI（http://localhost:8080）
        - Scheduler
        - Worker
        - Database（PostgreSQL）
```

- 確認服務是否啟動
```bash
docker compose ps
```

- 進入 Airflow UI（http://localhost:8080）測試 DAG

- 停止容器
```bash
docker compose down
```
- 刪除所有資料（⚠️會刪除所有 Airflow 設定）
```bash
docker compose down -v
```

- 手動啟動 Web Server
```bash
docker compose run airflow-webserver airflow webserver
```

###### 設定環境變數（可選）
- 若不想在 docker-compose.yml 中硬編碼資料庫密碼，可以使用 .env
```bash
echo "AIRFLOW_DB_USER=airflow" >> .env
echo "AIRFLOW_DB_PASSWORD=airflow" >> .env
```
- 然後修改 docker-compose.yml
```
environment:
  - AIRFLOW__DATABASE__SQL_ALCHEMY_CONN=postgresql+psycopg2://${AIRFLOW_DB_USER}:${AIRFLOW_DB_PASSWORD}@airflow-db/airflow
```

- 手動刷新 DAGs
```bash
docker exec -it airflow_pratice-airflow-webserver-1 airflow dags reserialize
```
- 直接 刪除 Web Server 的 cache
```bash
docker exec -it airflow_pratice-airflow-webserver-1 airflow dags list
docker exec -it airflow_pratice-airflow-webserver-1 airflow dags reserialize
docker compose restart airflow-webserver
```

- 測試 DAG 是否有效
```bash
docker exec -it airflow_pratice-airflow-webserver-1 airflow dags list
```
