---
name: dashboard-best-practices
version: v2.0.0
author: book-skills
description: 數據看板最佳實踐技能，掌握效能最佳化、狀態管理、錯誤處理和安全部署，建構生產級數據應用
---

# Dashboard Best Practices

## 任務目標
- 本 Skill 用於：遵循數據看板開發最佳實踐，建構生產級應用
- 能力包含：效能最佳化、狀態管理、錯誤處理、安全部署、測試策略
- 觸發條件：需要提升看板效能、可靠性和可維護性時

## 操作步驟

### 效能最佳化

#### 快取策略選擇
```python
import streamlit as st
import pandas as pd

# @st.cache_data：快取可序列化數據（DataFrame、字典等）
@st.cache_data(ttl=3600)  # 1小時過期
def load_data(source: str) -> pd.DataFrame:
    return pd.read_csv(source)

# @st.cache_resource：快取不可序列化物件（資料庫連線、ML模型）
@st.cache_resource
def get_db_connection():
    from sqlalchemy import create_engine
    return create_engine("sqlite:///data.db")

# 選擇指引：
# - 回傳 DataFrame/清單/字典 → @st.cache_data
# - 回傳資料庫連線/模型物件 → @st.cache_resource
# - 需要自動過期 → 設定 ttl 參數
```

#### 避免不必要的重算
```python
# 使用 session_state 快取中間計算結果
if 'processed_df' not in st.session_state:
    st.session_state.processed_df = heavy_processing(raw_df)

# 使用回呼函式避免 rerun
def on_filter_change():
    st.session_state.filter_applied = True

st.selectbox("類別", options, on_change=on_filter_change)
```

#### 查詢最佳化
```python
# 資料庫層預先聚合，減少傳輸數據量
@st.cache_data
def get_daily_summary():
    return pd.read_sql("""
        SELECT date, category, SUM(amount) as total
        FROM orders
        WHERE date >= :start_date
        GROUP BY date, category
    """, engine, params={"start_date": "2024-01-01"})
```

### 狀態管理

#### Session State 模式
```python
# 初始化模式
def init_state():
    defaults = {
        "filters": {"category": None, "date_range": None},
        "page": 1,
        "data": None,
    }
    for key, value in defaults.items():
        if key not in st.session_state:
            st.session_state[key] = value

init_state()

# 更新模式
def apply_filters(category, date_range):
    st.session_state.filters = {
        "category": category,
        "date_range": date_range,
    }
    st.session_state.page = 1  # 重置分頁
```

#### 跨頁面狀態共享
```python
# utils/state_manager.py
class AppState:
    """集中管理應用程式狀態"""
    
    @staticmethod
    def get(key, default=None):
        return st.session_state.get(key, default)
    
    @staticmethod
    def set(key, value):
        st.session_state[key] = value
    
    @staticmethod
    def clear_filters():
        st.session_state.filters = {"category": None, "date_range": None}
```

### 錯誤處理

#### 分層錯誤處理
```python
import streamlit as st
import pandas as pd

def load_data_safely(source: str) -> pd.DataFrame | None:
    """安全載入數據，回傳 None 表示失敗"""
    try:
        if source.endswith('.csv'):
            return pd.read_csv(source)
        elif source.endswith('.xlsx'):
            return pd.read_excel(source)
        else:
            st.error(f"不支援的檔案格式: {source}")
            return None
    except FileNotFoundError:
        st.error(f"檔案不存在: {source}")
        return None
    except pd.errors.EmptyDataError:
        st.warning("檔案為空")
        return None
    except Exception as e:
        st.error(f"載入失敗: {e}")
        return None

# 使用
df = load_data_safely("data.csv")
if df is None:
    st.stop()  # 停止後續執行
```

#### 使用者友善的錯誤提示
```python
# 使用不同層級的提示
st.error("嚴重錯誤，無法繼續")      # 紅色
st.warning("警告，結果可能不準確")   # 黃色
st.info("提示訊息")                # 藍色
st.success("操作成功")             # 綠色

# 例外展開（僅開發環境）
try:
    result = risky_operation()
except Exception as e:
    if st.secrets.get("debug", False):
        st.exception(e)  # 顯示完整堆疊
    else:
        st.error("處理失敗，請聯絡管理員")
```

### 數據安全

#### Secrets 管理
```python
# .streamlit/secrets.toml
[database]
host = "localhost"
port = 5432
user = "admin"
password = "your_password"

[api]
key = "your_api_key"

# 程式碼中使用
import streamlit as st

db_password = st.secrets["database"]["password"]
api_key = st.secrets["api"]["key"]
```

#### 輸入驗證
```python
def validate_date_range(start, end):
    if start > end:
        st.error("開始日期不能晚於結束日期")
        return False
    if (end - start).days > 365:
        st.warning("查詢範圍超過一年，載入可能較慢")
    return True

def validate_file_size(uploaded_file, max_mb=10):
    if uploaded_file.size > max_mb * 1024 * 1024:
        st.error(f"檔案大小超過限制 ({max_mb}MB)")
        return False
    return True
```

### 部署配置

#### Docker 部署
```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8501
HEALTHCHECK CMD curl --fail http://localhost:8501/_stcore/health

ENTRYPOINT ["streamlit", "run", "app.py", "--server.port=8501", "--server.address=0.0.0.0"]
```

#### docker-compose.yml
```yaml
services:
  dashboard:
    build: .
    ports:
      - "8501:8501"
    environment:
      - STREAMLIT_SERVER_HEADLESS=true
    volumes:
      - ./data:/app/data
    restart: unless-stopped
```

### 測試策略

#### Streamlit 應用測試
```python
# test_app.py
from streamlit.testing.v1 import AppTest

def test_app_loads():
    at = AppTest.from_file("app.py")
    at.run()
    assert not at.exception
    assert at.title[0].value == "數據看板"

def test_filter_interaction():
    at = AppTest.from_file("app.py")
    at.run()
    at.selectbox[0].select("科技").run()
    assert not at.exception
    assert len(at.dataframe) > 0
```

#### 數據處理測試
```python
# test_data_loader.py
import pandas as pd
import pytest
from utils.data_loader import load_data, clean_data

def test_load_csv():
    df = load_data("tests/fixtures/sample.csv")
    assert isinstance(df, pd.DataFrame)
    assert len(df) > 0

def test_clean_data_handles_missing():
    df = pd.DataFrame({"a": [1, None, 3]})
    cleaned = clean_data(df)
    assert cleaned["a"].isnull().sum() == 0
```

## 資源索引
- Streamlit 部署：https://docs.streamlit.io/deploy/
- Streamlit 測試：https://docs.streamlit.io/develop/api-reference/testing
- Streamlit 快取：https://docs.streamlit.io/develop/api-reference/caching
- Streamlit 安全：https://docs.streamlit.io/deploy/streamlit-community-cloud/share-your-app

## 注意事項
- 根據數據類型選擇合適的快取裝飾器
- 所有外部輸入都必須驗證
- 敏感資訊必須使用 secrets 管理
- 測試應涵蓋數據處理和介面互動
