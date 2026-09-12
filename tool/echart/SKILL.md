---
name: echart
version: v1.0.0
author: skill-factory
description: Apache ECharts 技能族，掌握折線圖、柱狀圖、餅圖、散點圖、關係圖、地圖、K線圖等各類圖表視覺化，適用於資料視覺化開發
tags: [echarts, visualization, chart, javascript, skill-family]
---

# EChart Skills - Apache ECharts 技能族

## 技能族概述

EChart Skills 是 Apache ECharts 技術棧的完整技能族，包含以下子技能：

- **echart-basic**：基礎圖表技能（折線圖、柱狀圖、餅圖、散點圖）
- **echart-relation**：關係圖技能（關係圖、桑基圖、樹圖、旭日圖）
- **echart-statistics**：統計圖技能（熱力圖、盒須圖、平行座標、矩陣）
- **echart-finance**：金融圖技能（K線圖、雷達圖、儀表盤）
- **echart-geo**：地理圖技能（地圖、3D地球、航班圖）
- **echart-3d**：3D圖表技能（3D柱狀圖、3D散點圖、3D曲面）
- **echart-advanced**：高階特性技能（dataset、dataZoom、自定義系列）
- **echart-multi**：多圖組合技能（grid、polar、timeline、聯動）

## 子技能列表

| 子技能 | 版本 | 描述 | 依賴 |
|--------|------|------|------|
| echart-basic | v1.0.0 | 基礎圖表（折線圖、柱狀圖、餅圖、散點圖） | 無 |
| echart-relation | v1.0.0 | 關係圖（關係圖、桑基圖、樹圖、旭日圖） | echart-basic |
| echart-statistics | v1.0.0 | 統計圖（熱力圖、盒須圖、平行座標、矩陣） | echart-basic |
| echart-finance | v1.0.0 | 金融圖（K線圖、雷達圖、儀表盤） | echart-basic |
| echart-geo | v1.0.0 | 地理圖（地圖、3D地球、3D地圖） | echart-basic |
| echart-3d | v1.0.0 | 3D圖表（3D柱狀圖、3D散點圖、3D曲面） | echart-basic |
| echart-advanced | v1.0.0 | 高階特性（dataset、dataZoom、自定義系列） | echart-basic |
| echart-multi | v1.0.0 | 多圖組合（grid、polar、timeline、聯動） | 多個基礎技能 |

## 使用方式

### 單獨使用子技能

```bash
# 使用 ECharts 基礎圖表技能
/ Skill echart-basic

# 使用 ECharts 關係圖技能
/ Skill echart-relation

# 使用 ECharts 金融圖技能
/ Skill echart-finance
```

### 使用完整技能族

```bash
# 使用 ECharts 全技能族
/ Skill echart
```

## 技能族結構

```
echart/
├── SKILL.md                    # 母技能定義
├── references/
│   └── overview.md            # 技能族概述
└── skills/                     # 子技能目錄
    ├── echart-basic/
    ├── echart-relation/
    ├── echart-statistics/
    ├── echart-finance/
    ├── echart-geo/
    ├── echart-3d/
    ├── echart-advanced/
    └── echart-multi/
```

## 學習路徑

1. **echart-basic**（先學）- 掌握折線圖、柱狀圖、餅圖、散點圖
2. **echart-finance** / **echart-statistics**（並行）- 根據需求選擇
3. **echart-relation** / **echart-geo**（並行）- 進階圖表
4. **echart-advanced** / **echart-multi**（後學）- 高階特性和組合

## 版本相容性

- ECharts 5.0+
- ECharts GL 1.0+
