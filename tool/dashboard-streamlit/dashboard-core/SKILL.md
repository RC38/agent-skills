---
name: dashboard-core
version: v3.0.0
author: book-skills
description: 數據看板核心技能，掌握專案架構、頁面導覽管理、數據流設計和模組化架構，實現高效的數據分析與展示應用
---

# Dashboard Core

## 任務目標
- 本 Skill 用於：搭建數據看板專案的整體架構
- 能力包含：專案初始化、目錄設計、頁面導覽（st.navigation/st.page_link）、側邊欄管理、數據流管理、模組化開發
- 觸發條件：需要從頭建立數據看板專案時

## 操作步驟

### 專案初始化
```bash
uv pip install streamlit streamlit-echarts pandas pyecharts

# 建立專案結構
mkdir dashboard_project
cd dashboard_project
touch app.py
mkdir pages/
mkdir components/
mkdir utils/
```

### 基礎目錄結構
```
dashboard_project/
├── app.py              # 主入口
├── pages/              # 多頁面
│   ├── overview.py
│   ├── analysis.py
│   └── report.py
├── components/         # 元件
│   ├── charts.py
│   └── tables.py
├── utils/             # 工具
│   ├── data_loader.py
│   └── formatters.py
└── .streamlit/
    └── config.toml
```

### Streamlit 設定
```toml
# .streamlit/config.toml
[general]
title = "數據看板"
favicon = "🏠"

[theme]
primaryColor = "#0078D4"
backgroundColor = "#FFFFFF"
secondaryBackgroundColor = "#F5F5F5"
textColor = "#262730"
font = "sans serif"

[server]
headless = true
```

### 頁面導覽：st.navigation 方式

```python
# app.py - 使用 st.navigation 實現靈活的多頁面應用
import streamlit as st

st.set_page_config(page_title="數據看板", page_icon="📊", layout="wide")

# 定義所有頁面
pages = {
    "數據概覽": [
        st.Page("pages/01_overview.py", title="總覽", icon="🏠"),
        st.Page("pages/02_metrics.py", title="核心指標", icon="📈"),
    ],
    "數據分析": [
        st.Page("pages/03_analysis.py", title="銷售分析", icon="🔍"),
        st.Page("pages/04_comparison.py", title="同期比較", icon="📊"),
    ],
    "系統設定": [
        st.Page("pages/05_settings.py", title="偏好設定", icon="⚙️"),
    ],
}

# 執行導覽
pg = st.navigation(pages)
pg.run()
```

### 頁面導覽：pages/ 目錄方式

```
# 快速建立多頁面應用（Streamlit 自動識別）
dashboard_project/
├── app.py              # 主入口（首頁）
└── pages/              # 頁面目錄
    ├── 1_📊_概覽.py      # 顯示為 "📊 概覽"
    ├── 2_📈_分析.py      # 顯示為 "📈 分析"
    ├── 3_📋_報表.py      # 顯示為 "📋 報表"
    └── 4_⚙️_設定.py      # 顯示為 "⚙️ 設定"
```

```python
# 每個頁面檔案的標頭設定
# pages/1_📊_概覽.py
import streamlit as st

st.set_page_config(
    page_title="數據概覽",
    page_icon="📊",
    layout="wide"
)

st.title("📊 數據概覽")
# 頁面內容...
```

### 自訂側邊欄導覽

```python
# app.py - 使用 st.page_link 建立自訂導覽
import streamlit as st

st.set_page_config(page_title="數據看板", layout="wide")

# 隱藏預設側邊欄導覽（在 .streamlit/config.toml 中設定）
# [client]
# showSidebarNavigation = false

with st.sidebar:
    st.title("📊 數據看板")
    st.divider()
    
    st.page_link("app.py", label="首頁", icon="🏠")
    st.page_link("pages/1_📊_概覽.py", label="數據概覽", icon="📊")
    st.page_link("pages/2_📈_分析.py", label="銷售分析", icon="📈")
    st.page_link("pages/3_📋_報表.py", label="報表匯出", icon="📋")
    
    st.divider()
    st.page_link("pages/4_⚙️_設定.py", label="設定", icon="⚙️")

# 主內容區域
st.title("歡迎使用數據看板")
```

### 動態導覽（基於角色/權限）

```python
# menu.py - 動態導覽選單
import streamlit as st

def show_menu():
    """根據使用者角色顯示不同的導覽選單"""
    with st.sidebar:
        st.title("📊 數據看板")
        st.divider()
        
        # 所有使用者可見
        st.page_link("app.py", label="首頁", icon="🏠")
        st.page_link("pages/1_📊_概覽.py", label="數據概覽", icon="📊")
        
        # 僅分析師和管理員可見
        if st.session_state.get("role") in ["analyst", "admin"]:
            st.page_link("pages/2_📈_分析.py", label="銷售分析", icon="📈")
        
        # 僅管理員可見
        if st.session_state.get("role") == "admin":
            st.page_link("pages/4_⚙️_設定.py", label="設定", icon="⚙️")

# app.py 中使用
from menu import show_menu

if "role" not in st.session_state:
    st.session_state.role = "viewer"

# 角色選擇器（僅用於示範）
with st.sidebar:
    role = st.selectbox("模擬角色", ["viewer", "analyst", "admin"])
    st.session_state.role = role

show_menu()
```

### 帶標籤分組的側邊欄

```python
# app.py - 使用 Section 分組導覽
import streamlit as st

st.set_page_config(page_title="數據看板", layout="wide")

# 方式一：使用字典分組（st.navigation 支援）
sections = {
    "核心功能": [
        st.Page("pages/1_📊_概覽.py", title="數據概覽", icon="📊"),
        st.Page("pages/2_📈_分析.py", title="銷售分析", icon="📈"),
    ],
    "報告中心": [
        st.Page("pages/3_📋_報表.py", title="報表匯出", icon="📋"),
        st.Page("pages/3b_📑_模板.py", title="範本管理", icon="📑"),
    ],
    "系統": [
        st.Page("pages/4_⚙️_設定.py", title="偏好設定", icon="⚙️"),
    ],
}

pg = st.navigation(sections)
pg.run()
```

### 數據載入模式
```python
# utils/data_loader.py
import streamlit as st
import pandas as pd

@st.cache_data
def load_data(source: str) -> pd.DataFrame:
    if source.endswith('.csv'):
        return pd.read_csv(source)
    elif source.endswith('.xlsx'):
        return pd.read_excel(source)
    else:
        raise ValueError(f"Unsupported source: {source}")

# 使用範例
df = load_data("data/sales.csv")
```

### 數據快取策略
```python
import streamlit as st

# 快取數據函式
@st.cache_data(ttl=3600)  # 1小時過期
def get_sales_data():
    return pd.read_csv("sales.csv")

# 快取資源（如ML模型）
@st.cache_resource
def init_model():
    return load_ml_model("model.pkl")
```

### Session State 管理
```python
import streamlit as st

# 初始化 session state
if 'df' not in st.session_state:
    st.session_state.df = None

if st.session_state.df is None:
    st.session_state.df = load_data("data.csv")
```

## 資源索引
- Streamlit 官方文件：https://docs.streamlit.io/
- Streamlit 多頁面：https://docs.streamlit.io/develop/concepts/multipage-apps
- 設定參考：https://docs.streamlit.io/develop/api-reference/configuration/config.toml

## 注意事項
- 使用 @st.cache_data 快取耗時數據載入操作
- Session State 用於跨 rerun 保持使用者狀態
- 合理拆分頁面，每個頁面職責單一
- 使用子目錄組織頁面和元件
