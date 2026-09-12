---
name: dashboard-echarts
version: v2.0.0
author: book-skills
description: ECharts 視覺化技能，掌握 PyECharts 和 Streamlit-ECharts 的圖表配置，實現豐富的互動式數據視覺化
---

# Dashboard ECharts

## 任務目標
- 本 Skill 用於：使用 ECharts 建立互動式圖表
- 能力包含：PyECharts 圖表、Streamlit-ECharts 整合、圖表配置
- 觸發條件：需要在看板中展示複雜互動圖表時

## 操作步驟

### 安裝依賴
```bash
uv pip install streamlit-echarts pyecharts
# 或
uv pip install streamlit-echarts[pyecharts]
```

### 基礎折線圖
```python
import streamlit as st
from streamlit_echarts import st_echarts

options = {
    "xAxis": {
        "type": "category",
        "data": ["週一", "週二", "週三", "週四", "週五", "週六", "週日"]
    },
    "yAxis": {"type": "value"},
    "series": [{
        "data": [820, 932, 901, 934, 1290, 1330, 1320],
        "type": "line",
        "smooth": True
    }]
}

st_echarts(options=options, height="400px")
```

### 柱狀圖
```python
options = {
    "xAxis": {"type": "category", "data": ["A", "B", "C", "D"]},
    "yAxis": {"type": "value"},
    "series": [{
        "data": [120, 200, 150, 80],
        "type": "bar",
        "itemStyle": {"color": "#5470C6"}
    }]
}
st_echarts(options=options)
```

### 圓餅圖
```python
options = {
    "series": [{
        "type": "pie",
        "radius": ["40%", "70%"],
        "data": [
            {"value": 1048, "name": "搜尋引擎"},
            {"value": 735, "name": "直接造訪"},
            {"value": 580, "name": "電子郵件行銷"}
        ],
        "label": {"show": True, "formatter": "{b}: {c} ({d}%)"}
    }]
}
st_echarts(options=options)
```

### 散佈圖
```python
import random
data = [[random.randint(1, 100) for _ in range(10)] for _ in range(3)]

options = {
    "xAxis": {"type": "value"},
    "yAxis": {"type": "value"},
    "series": [{
        "type": "scatter",
        "symbolSize": 20,
        "data": data[0],
        "itemStyle": {"color": "#5470C6"}
    }]
}
st_echarts(options=options, height="500px")
```

### 多系列圖表
```python
options = {
    "legend": {"data": ["蒸發量", "降水量"]},
    "xAxis": {"type": "category", "data": ["1月", "2月", "3月", "4月", "5月"]},
    "yAxis": {"type": "value"},
    "series": [
        {
            "name": "蒸發量",
            "type": "bar",
            "data": [2.0, 4.9, 7.0, 23.2, 25.6]
        },
        {
            "name": "降水量",
            "type": "bar",
            "data": [2.6, 5.9, 9.0, 26.4, 28.7]
        }
    ]
}
st_echarts(options=options)
```

### PyECharts 方式
```python
from pyecharts import options as opts
from pyecharts.charts import Bar, Line
from streamlit_echarts import st_pyecharts

# 使用 PyECharts 建構圖表
bar = (
    Bar()
    .add_xaxis(["Microsoft", "Amazon", "IBM", "Oracle", "Google"])
    .add_yaxis("2023營收(億)", [2100, 1850, 650, 520, 1820])
    .set_global_opts(
        title_opts=opts.TitleOpts(title="雲端服務商營收對比"),
        toolbox_opts=opts.ToolboxOpts(),
        legend_opts=opts.LegendOpts(selected_mode="single")
    )
)
st_pyecharts(bar, height="400px")
```

### 動態互動
```python
from streamlit_echarts import st_echarts

options = {
    "tooltip": {"trigger": "axis"},
    "legend": {"data": ["銷量"]},
    "xAxis": {"type": "category", "data": ["襯衫", "毛衣", "領帶", "褲子", "高跟鞋"]},
    "yAxis": {"type": "value"},
    "series": [{"data": [5, 20, 36, 10, 10], "type": "line"}]
}

# 新增點擊事件
events = {
    "click": "function(params) { return params.name; }"
}
result = st_echarts(options=options, events=events, key="chart1")
st.write(f"點擊了: {result}")
```

### 地圖視覺化
```python
from pyecharts import options as opts
from pyecharts.charts import Map
from streamlit_echarts import st_pyecharts

# 中國地圖範例
china_map = (
    Map()
    .add("銷售額", 
         [("廣東", 500), ("北京", 350), ("上海", 420), ("浙江", 380)], 
         "china")
    .set_global_opts(
        title_opts=opts.TitleOpts(title="中國地圖"),
        visualmap_opts=opts.VisualMapOpts(max_=500)
    )
)
st_pyecharts(china_map, height="500px")
```

### 主題配置
```python
# 深色主題
st_echarts(options=options, theme="dark", height="400px")

# 自訂主題色彩
custom_theme = {
    "color": ["#5470C6", "#91CC75", "#FAC858", "#EE6666"]
}
st_echarts(options=options, theme=custom_theme)
```

### 響應式尺寸
```python
st_echarts(
    options=options,
    height="400px",      # 高度
    width="100%",        # 寬度
    renderer="canvas"   # 或 "svg"
)
```

## 常用配置

### 標題與工具箱
```python
opts.TitleOpts(
    title="主標題",
    subtitle="副標題",
    pos_left="center"
)

opts.ToolboxOpts(
    feature=opts.ToolBoxFeatureSaveAsImage()
)
```

### 圖例配置
```python
opts.LegendOpts(
    data=["系列1", "系列2"],
    selected_mode=False  # 停用圖例點擊
)
```

### 提示框
```python
opts.TooltipOpts(
    trigger="item",  # 或 "axis"
    trigger_on="mousemove",
    formatter="{b}: {c}"
)
```

---

## 與進階 EChart 技能族協同 (Integration with `tool/echart`)

在 Streamlit 中使用 `st_echarts(options=options)` 時，其 `options` 參數在結構上**完全對應原生的 Apache ECharts 配置項字典**。

當 Streamlit 看板需要超越基礎折線/柱狀/圓餅圖，製作**桑基圖、關係網絡、統計熱力圖、地理地圖、金融 K 線、3D 圖表或多圖聯動**時，**強烈推薦關聯並調用專門的 ECharts 技能族**：[`tool/echart`](file:///Volumes/tf-1tb/googledriver/96.code/github/skills/agent-skills/tool/echart/SKILL.md)。

### 如何在 Streamlit 中直接套用 `tool/echart` 的配置
1. 在 `tool/echart` 中選定所需的專業圖表類型（如 `echart-relation` 的桑基圖或 `echart-statistics` 的熱力圖）。
2. 直接將其 JSON / Dict 配置傳遞給 `st_echarts(options=options)` 即可完成渲染。
3. 範例：
```python
import streamlit as st
from streamlit_echarts import st_echarts

# 複用來自 tool/echart 的進階圖表 option 結構
sankey_option = {
    "tooltip": {"trigger": "item", "triggerOn": "mousemove"},
    "series": [{
        "type": "sankey",  # 桑基圖配置，詳見 tool/echart/skills/echart-relation
        "data": [{"name": "首頁"}, {"name": "購物車"}, {"name": "結帳"}, {"name": "流失"}],
        "links": [
            {"source": "首頁", "target": "購物車", "value": 800},
            {"source": "首頁", "target": "流失", "value": 200},
            {"source": "購物車", "target": "結帳", "value": 600},
            {"source": "購物車", "target": "流失", "value": 200}
        ]
    }]
}

st_echarts(options=sankey_option, height="450px")
```

### 進階圖表查找索引

| Streamlit 看板進階展示需求 | 推薦調用之 `tool/echart` 子技能 | 快速參考路徑 |
| :--- | :--- | :--- |
| **用戶漏斗流向、層級關係** | `echart-relation`（桑基圖、樹圖、關係圖） | [`tool/echart/skills/echart-relation/`](file:///Volumes/tf-1tb/googledriver/96.code/github/skills/agent-skills/tool/echart/skills/echart-relation/) |
| **區域熱力圖、統計盒須圖、平行座標** | `echart-statistics`（熱力圖、盒須圖） | [`tool/echart/skills/echart-statistics/`](file:///Volumes/tf-1tb/googledriver/96.code/github/skills/agent-skills/tool/echart/skills/echart-statistics/) |
| **股票 K 線、布林通道、雷達指標** | `echart-finance`（K線圖、雷達圖、儀表盤） | [`tool/echart/skills/echart-finance/`](file:///Volumes/tf-1tb/googledriver/96.code/github/skills/agent-skills/tool/echart/skills/echart-finance/) |
| **全台/全球地圖、GPS 軌跡** | `echart-geo`（地圖、3D地球、飛線圖） | [`tool/echart/skills/echart-geo/`](file:///Volumes/tf-1tb/googledriver/96.code/github/skills/agent-skills/tool/echart/skills/echart-geo/) |
| **3D 柱狀、3D 散點、3D 曲面** | `echart-3d`（3D 圖表） | [`tool/echart/skills/echart-3d/`](file:///Volumes/tf-1tb/googledriver/96.code/github/skills/agent-skills/tool/echart/skills/echart-3d/) |
| **大數據 dataset 抽象、DataZoom 滑塊** | `echart-advanced`（dataset、dataZoom） | [`tool/echart/skills/echart-advanced/`](file:///Volumes/tf-1tb/googledriver/96.code/github/skills/agent-skills/tool/echart/skills/echart-advanced/) |
| **多圖表網格聯動** | `echart-multi`（Grid 多圖聯動） | [`tool/echart/skills/echart-multi/`](file:///Volumes/tf-1tb/googledriver/96.code/github/skills/agent-skills/tool/echart/skills/echart-multi/) |

> [!TIP]
> 完整 365 個官方範例清單與直達連結，請直接查閱：[`tool/echart/references/範例清單.md`](file:///Volumes/tf-1tb/googledriver/96.code/github/skills/agent-skills/tool/echart/references/範例清單.md)。

---

## 資源索引
- Streamlit-ECharts：https://github.com/andfanilo/streamlit-echarts
- PyECharts 文件：https://pyecharts.readthedocs.io/
- ECharts 範例：https://echarts.apache.org/examples/
- ECharts 全技能族：[`tool/echart`](file:///Volumes/tf-1tb/googledriver/96.code/github/skills/agent-skills/tool/echart/SKILL.md)

## 注意事項
- 使用 PyECharts 建構複雜圖表更方便
- st_echarts 支援原生 ECharts 配置（可無縫相容 `tool/echart` 產生的所有 option 字典）
- height 和 width 支援 CSS 單位
- renderer="svg" 更適合列印和無障礙輔助功能
- 使用 on_select 參數處理選取事件

