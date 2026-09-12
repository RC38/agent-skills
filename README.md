# Agent Skills

AI Agent 技能集合 — 為 AI 程式設計助手設計的專業技能庫。

## 分類

### lang/ — 語言與框架（8）
教你掌握特定程式語言或開發框架。

| 技能 | 說明 |
|------|------|
| [flutter](lang/flutter/) | Flutter 開發：Clean Architecture、TDD、BLoC |
| [rust](lang/rust/) | Rust 全端開發指導 |
| [moonbit](lang/moonbit/) | MoonBit AI 原生程式語言 |
| [svelte](lang/svelte/) | Svelte 5 核心概念、Runes 系統 |
| [tauri](lang/tauri/) | Tauri v2 桌面應用開發 |
| [wxt](lang/wxt/) | WXT 瀏覽器擴充功能開發框架 |
| [cocos](lang/cocos/) | Cocos Creator 3.8 遊戲引擎開發 |
| [vsce](lang/vsce/) | VSCode 擴充功能開發完整指南 |

### tool/ — 工具（8）
教你使用特定開發工具或函式庫。

| 技能 | 說明 |
|------|------|
| [git](tool/git/) | Git 版本控制全流程 |
| [agent](tool/agent/) | Hermes Agent 設定系統 |
| [drissonpage](tool/drissonpage/) | DrissionPage 網頁自動化 |
| [scrapling](tool/scrapling/) | Scrapling 自適應網頁爬取框架 |
| [venvstacks](tool/venvstacks/) | 分層 Python 虛擬環境堆疊 |
| [plotnine](tool/plotnine/) | Plotnine 資料視覺化 |
| [echart](tool/echart/) | Apache ECharts 圖表視覺化 |
| [dashboard](tool/dashboard/) | Streamlit 資料儀表板建構 |

### process/ — 流程與方法論（10）
教你系統化的開發流程、架構方法論與編碼規範。

| 技能 | 說明 |
|------|------|
| [project-management](process/project-management/) | 全流程專案管理母技能（含 ROADMAP/TODO/OKR 規劃子技能） |
| [six-layer-architect](process/six-layer-architect/) | 六層架構全端產生器 |
| [software-design](process/software-design/) | 軟體設計與編碼規範（含程式碼最佳化 + 設計模式子技能） |
| [python-team](process/python-team/) | Python 四角色團隊協同開發 |
| [pythonic-style](process/pythonic-style/) | Python 程式碼風格與慣用法 |
| [agents-writer](process/agents-writer/) | AGENTS.md 寫作專家 |
| [data-analytics](process/data-analytics/) | 資料分析完整技能體系 |
| [tutorial-writer](process/tutorial-writer/) | 教材撰寫 5-Sub Router |
| [personal-software-dev-exploration](process/personal-software-dev-exploration/) | 個人軟體開發探索方法論 |
| [doc-orchestrator](process/doc-orchestrator/) | 文件編排操盤手（完整生命週期管理軟體開發文件） |

### utility/ — 輔助工具（4）
跨領域工具與輔助決策技能。

| 技能 | 說明 |
|------|------|
| [tech-comparison](utility/tech-comparison/) | 技術選型對比助手 |
| [side-hustle-evaluator](utility/side-hustle-evaluator/) | 副業評估決策工具 |
| [recruitment-processor](utility/recruitment-processor/) | 徵才資訊處理 |
| [copyright-assist](utility/copyright-assist/) | 軟著申請輔助（中國版權保護中心全流程） |

## 快速開始

每個技能目錄下有獨立的 `SKILL.md`，包含完整的說明文件。

```bash
# 列出所有技能
Get-ChildItem -Recurse -Filter "SKILL.md" -Depth 2 | ForEach-Object { $_.Directory.Name }

# 檢視技能分類統計
Get-ChildItem -Directory | ForEach-Object { "$($_.Name): $(@(Get-ChildItem $_.FullName -Directory).Count)" }
```

## 目錄分類規則

新增技能時，依以下規則歸類：

| 目錄 | 條件 | 範例 |
|------|------|------|
| `lang/` | 教學內容為某程式語言或完整開發框架 | Flutter, Rust, Svelte, Tauri |
| `tool/` | 教學內容為某具體工具或函式庫 | Git, DrissionPage, ECharts |
| `process/` | 教學內容為開發流程、架構方法或編碼規範 | TDD, Clean Architecture, 專案管理 |
| `utility/` | 不屬於以上任何類別的通用輔助技能 | 技術選型, 副業評估, 軟著申請 |

## 命名規範

- 所有技能目錄統一命名為 `{name}`（全小寫、連字號分隔）
- 技能檔案統一使用 `SKILL.md`
- 儲存庫根目錄不放置任何技能檔案，僅放置中繼資料檔案

## 品質閘門

- [ ] 技能目錄位於正確的類別下
- [ ] 目錄名稱符合 `{name}` 格式
- [ ] 包含 `SKILL.md` 主文件
- [ ] SKILL.md 包含 name/version/description/tags 中繼資料
- [ ] 無跨儲存庫重複（舊儲存庫已棄用）

## 舊儲存庫

以下舊儲存庫已棄用，請使用本儲存庫：

| 舊儲存庫 | 狀態 |
|--------|------|
| morning-start/book-skills | ❌ 棄用 |
| morning-start/coze-skills | ❌ 棄用 |
| morning-start/wiki-skills | ❌ 棄用 |
| morning-start/dev-skills | ❌ 棄用 |
