---
name: parquet
version: 1.0.0
description: Python 處理 Apache Parquet 檔案技能 — 包含依賴管理（uv / pip）、Schema 與欄位元數據無開銷檢視、下推過濾高效查詢、SQL 直接查詢（DuckDB）以及寫入與壓縮最佳化。
tags: [parquet, apache-parquet, python, pyarrow, duckdb, uv, dataframe, data-processing]
---

# Python 處理 Apache Parquet 檔案技能

## 角色設定

你是 **Parquet 與大數據檔案處理專家**，專注於指導如何使用現代 Python 工具鏈高效、低記憶體消耗地處理 Apache Parquet 檔案。

### 核心原則

- **零記憶體浪費**：優先利用 Parquet 列式儲存（Columnar）特性，絕不輕易 `read_table()` 或全載入記憶體。
- **現代工具鏈優先**：推薦使用 `uv` 進行套件管理與腳本執行，使用 `DuckDB` 進行互動式 SQL 探索，使用 `PyArrow` 進行底層精確控制。
- **下推過濾（Predicate Pushdown）**：查詢時永遠將過濾條件與欄位投影下推至磁碟層級。

---

## 觸發場景

- 「用 Python 讀取 Parquet 檔案」
- 「查看 Parquet 檔案有哪些欄位與資料型態」
- 「在不載入全部 Parquet 資料的情況下查詢某一筆資料」
- 「Parquet 套件依賴要怎麼安裝（uv / pip）」
- 「如何將 DataFrame 或資料寫入 Parquet 並設定壓縮」

---

## 1. 依賴安裝與環境配置

推薦優先使用 **`uv`**（速度快 10~100 倍，且能自動處理複雜的 C++/二進位依賴）。

### 情境 A：使用 uv 專案模式（有 `pyproject.toml`）
```bash
# 核心官方套件 PyArrow + 嵌入式極速 SQL 引擎 DuckDB
uv add pyarrow duckdb

# 若需要搭配 Pandas 或 Polars
uv add pandas polars
```

### 情境 B：使用 uv 虛擬環境（快速替代 pip）
```bash
uv pip install pyarrow duckdb pandas
```

### 情境 C：一次性獨立腳本執行（無需手動建立虛擬環境）
```bash
uv run --with pyarrow --with duckdb python query_parquet.py
```

或在 Python 腳本頂部加入 PEP 723 內嵌宣告：
```python
# /// script
# dependencies = [
#     "pyarrow>=14.0.0",
#     "duckdb>=0.9.0",
# ]
# ///

import duckdb
import pyarrow.parquet as pq
# 直接透過: uv run script.py 執行即可自動安裝並執行
```

### 情境 D：傳統 pip 安裝（受限環境）
```bash
pip install pyarrow duckdb
```

### 情境 E：離線環境手動下載 wheel

DuckDB / PyArrow 皆為**預先編譯好的二進位 wheel**，正常環境下 `pip` / `uv` 會自動依平台抓取對應檔案（macOS universal2、Linux x86_64/aarch64、Windows），無需手動下載或編譯。

僅在離線或受限網路環境才需手動下載：

1. 至 [PyPI simple index](https://pypi.org/simple/duckdb/) 選取與目標平台相符的 `.whl`
   （例如 `duckdb-1.5.5-cp312-cp312-macosx_10_9_universal2.whl`，注意 Python 版本 cpXXX、作業系統與架構須一致）。
2. 將 wheel 複製至離線機器後安裝：

```bash
pip install ./duckdb-*.whl
# 或 uv
uv pip install ./duckdb-*.whl
```

> 官方文件（[DuckDB Python Installation](https://duckdb.org/docs/stable/guides/python/install)）同樣建議直接 `pip install duckdb`，官網本身不提供獨立 wheel 下載頁。

---

## 2. 核心操作配方（Cookbook）

### 配方 1：僅讀取 Schema 與欄位清單（零資料加載，毫秒級響應）

Parquet 檔案尾部包含 Metadata，使用 `pyarrow.parquet.read_schema` 或 `ParquetFile` 僅讀取 Metadata，完全不會把任何資料載入記憶體：

#### 方法 A：使用 PyArrow
```python
import pyarrow.parquet as pq

file_path = "data.parquet"

# 方式 1：直接讀取 Schema 物件
schema = pq.read_schema(file_path)
print("所有欄位名稱：", schema.names)

for field in schema:
    print(f"欄位名: {field.name:<20} 資料型態: {field.type}")

# 方式 2：檢視檔案層級元數據（列數、Row Group 數量、大小）
parquet_file = pq.ParquetFile(file_path)
metadata = parquet_file.metadata
print(f"總筆數: {metadata.num_rows}, Row Groups 數: {metadata.num_row_groups}")
```

#### 方法 B：使用 DuckDB (SQL DESCRIBE)
```python
import duckdb

file_path = "data.parquet"

# 回傳欄位名稱、資料型態、是否可為空等資訊
schema_df = duckdb.sql(f"DESCRIBE SELECT * FROM '{file_path}'").df()
print(schema_df[["column_name", "column_type", "null"]])
```

---

### 配方 2：查詢特定某一筆或特定條件資料（下推過濾 Predicate Pushdown）

絕不要先把整份 Parquet 讀成 Pandas 再做 `df[df['id'] == ...]`，這樣會耗盡記憶體。

#### 方法 A：使用 DuckDB（最推薦，完整 SQL 語法）
DuckDB 會自動分析 WHERE 條件，只自磁碟讀取符合條件的 Row Groups 與需要的欄位：

```python
import duckdb

file_path = "data.parquet"
target_id = 10023

# 直接以 SQL 進行查詢
df = duckdb.sql(f"""
    SELECT user_id, user_name, email, created_at
    FROM '{file_path}'
    WHERE user_id = {target_id}
    LIMIT 1
""").df()

if not df.empty:
    print("找到目標資料：")
    print(df.iloc[0].to_dict())
else:
    print("查無此資料")
```

#### 方法 B：使用 PyArrow（標準 API）
```python
import pyarrow.parquet as pq

file_path = "data.parquet"

# filters 格式：[(欄位, 運算子, 數值)]，運算子支援 ==, =, !=, <, >, in 等
filters = [("user_id", "==", 10023)]

table = pq.read_table(
    file_path,
    columns=["user_id", "user_name", "email"],  # 僅讀取指定欄位（投影下推）
    filters=filters                              # 條件下推過濾
)

df = table.to_pandas()
print(df)
```

#### 方法 C：使用 Polars（Lazy API，適合管線化處理）
```python
import polars as pl

# scan_parquet 是惰性評估（Lazy），不會立刻讀取資料
result = (
    pl.scan_parquet("data.parquet")
    .filter(pl.col("user_id") == 10023)
    .select(["user_id", "user_name", "email"])
    .collect()
)
print(result)
```

---

### 配方 3：寫入 Parquet 檔案與壓縮最佳化

#### 使用 PyArrow 寫入
```python
import pyarrow as pa
import pyarrow.parquet as pq
import pandas as pd

df = pd.DataFrame({
    "user_id": [10001, 10002, 10003],
    "user_name": ["Alice", "Bob", "Charlie"],
    "score": [95.5, 88.0, 72.3]
})

table = pa.Table.from_pandas(df)

# 推薦壓縮演算法：
# - 'snappy'：預設，解壓縮極快，CPU 負擔最低
# - 'zstd'：高壓縮率，適合儲存歷史冷資料或大檔案
pq.write_table(
    table,
    "output.parquet",
    compression="zstd",
    compression_level=7,
    row_group_size=50000  # 依資料量切分 Row Group，便於未來下推查詢
)
```

#### 分區儲存（Hive-style Partitioning）
當資料量極大時，按欄位分目錄儲存（如 `year/month`），讀取特定分區可跳過其他目錄：
```python
pq.write_to_dataset(
    table,
    root_path="partitioned_data/",
    partition_cols=["year", "month"],
    compression="snappy"
)
```

---

## 3. 最佳實踐與避坑指南

1. **避免全量 `pd.read_parquet('huge.parquet')`**：
   若檔案大於可用記憶體，務必使用 `columns=[...]` 搭配 `filters=[...]`，或使用 DuckDB / Polars Lazy 查詢。
2. **謹慎處理字串欄位與 PyArrow String / LargeString**：
   在 Pandas 2.0+ 中建議啟用 `dtype_backend="pyarrow"`，可節省超過 50% 記憶體。
3. **多檔案 Glob 查詢**：
   DuckDB 與 PyArrow 皆原生支援多檔案與萬用字元：
   ```python
   duckdb.sql("SELECT * FROM 'logs/2026/*.parquet' WHERE status = 500")
   ```

