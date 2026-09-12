---
name: dashboard
version: v3.0.0
author: book-skills
description: 數據看板技能庫，使用 Python + Streamlit + ECharts + Pandas 快速建構數據分析和視覺化應用，掌握從數據處理到介面展示的完整技能體系
---
# Dashboard Skills

## 任務目標

- 本 Skill 用於：使用 Python 快速建構數據看板和視覺化應用
- 能力包含：Streamlit 介面開發、Pandas 數據處理、ECharts 圖表視覺化、專案架構設計、頁面導覽管理、生產最佳實踐
- 觸發條件：需要快速建立數據分析和展示應用時

## 技能地圖

### 基礎技能

- [dashboard-core](dashboard-core/) - 核心架構：專案初始化、頁面導覽（st.navigation/st.page_link）、側邊欄、快取策略
- [dashboard-streamlit](dashboard-streamlit/) - Streamlit 基礎：文字、表格、圖表、輸入元件、版面配置容器
- [dashboard-pandas](dashboard-pandas/) - Pandas 數據處理：數據讀取、清理、轉換、聚合

### 進階技能

- [dashboard-echarts](dashboard-echarts/) - ECharts 視覺化：PyECharts、Streamlit-ECharts、互動式圖表整合
- [tool/echart](../echart/) - Apache ECharts 全技能族（進階）：桑基圖、統計熱力圖、地理地圖、3D圖表、金融K線與365個官方範例（產生的 option 字典可直接傳遞給 st_echarts）

### 工程技能

- [dashboard-best-practices](dashboard-best-practices/) - 最佳實踐：效能最佳化、狀態管理、錯誤處理、安全部署、測試

## 學習路徑

### 快速入門

1. [dashboard-core](dashboard-core/) - 了解專案結構
2. [dashboard-streamlit](dashboard-streamlit/) - 掌握基礎元件
3. [dashboard-pandas](dashboard-pandas/) - 數據處理基礎

### 圖表進階

4. [dashboard-echarts](dashboard-echarts/) - ECharts 進階圖表

### 生產部署

5. [dashboard-best-practices](dashboard-best-practices/) - 效能與部署

## 快速開始

### 安裝依賴

```bash
uv pip install streamlit streamlit-echarts pandas pyecharts
```

### 建立第一個看板

```python
import streamlit as st
import pandas as pd
from streamlit_echarts import st_echarts

st.title("我的數據看板")

# 讀取數據
df = pd.read_csv("sales.csv")

# 顯示數據
st.dataframe(df)

# 綁定篩選
with st.sidebar:
    category = st.selectbox("選擇類別", df['category'].unique())

# 圖表
options = {
    "xAxis": {"type": "category", "data": df['date'].tolist()},
    "yAxis": {"type": "value"},
    "series": [{"data": df['sales'].tolist(), "type": "line"}]
}
st_echarts(options=options)
```

## 資源索引

- Streamlit 文件：https://docs.streamlit.io/
- PyECharts：https://pyecharts.readthedocs.io/
- Streamlit-ECharts：https://github.com/andfanilo/streamlit-echarts
- Pandas 文件：https://pandas.pydata.org/

## 注意事項

- 使用 uv 管理 Python 依賴
- 合理使用 @st.cache_data 快取
- Session State 管理跨 rerun 狀態
- 生產環境使用 secrets 管理敏感設定
