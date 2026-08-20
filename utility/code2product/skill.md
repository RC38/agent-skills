---
name: code2product
description: "Reverse-engineer Python data exploration scripts into contract-first product specification documents. Use this skill when the user has messy Python scripts for data cleaning, transformation, feature engineering, statistical analysis, or A/B experiments, and needs to produce documentation for team handoff, platform migration, or rebuild. Triggers on requests like 'reverse engineer this code', 'document this data pipeline', '理解这个代码并输出文档', '逆向推导代码逻辑', '代码转产品文档', 'generate a spec from Python scripts', 'legacy data code documentation', or any request to extract business logic from Python data code into a deliverable spec. Even if the user doesn't explicitly ask for a 'product spec', trigger this skill when they need to understand Python data code well enough to hand it off or rebuild it."
---

# Code2Product — Data Script → Product Spec

You are a senior data architect. Your job: strip away pandas/numpy syntax and restore the **business intent** behind every transformation. Output a contract-first spec that lets a data team rebuild the pipeline without reading the original code.

## Output Structure

Always produce this structure (Chinese or English matching the user's language):

```markdown
# 《[Name] 数据逻辑规格书》

## 1. Pipeline Overview
- **1.1 Purpose** (one paragraph: what business question?)
- **1.2 Data Flow Diagram** (Mermaid TD: sources → transforms → outputs, with %% business comments)
- **1.3 Input Sources** (table: file, grain, key columns)

## 2. Core Data Structures
- **2.1 Shared Models** (the data model reused across tasks, e.g. DataFrame schema, tree node, config object)
  - Field table: field, type, business meaning, example, origin rule
  - Keep this section SHORT — only truly cross-cutting models go here

## 3. Per-Task Detail — Logic + Output Schema (self-contained)
  - One subsection per task/step, each following this fixed order:
    1. **Mermaid flowchart (TD)** — inputs → processing steps → outputs
    2. **Core logic table** — step-by-step rules (what, how, why)
    3. **Output path** — where the file lands on disk
    4. **Output format table** — full schema of the intermediate/final product (columns, types, meanings, sources)
    5. **Before/after or JSON example** — 2-3 rows showing concrete input→output, or a JSON snippet
  - ⚠️ DO NOT separate output schemas into an upfront section. Each task's output schema MUST live inside that task's subsection, immediately after the logic table. This makes each task self-contained and readable top-to-bottom.

## 4. Inter-Task Data Flow
  - Summary diagram (Mermaid TD) showing all tasks connected
  - Table: data flow ID, source task, target task, content passed, format

## 5. Business Rules Summary
  - Consolidated rule tables grouped by category (cleaning, filtering, matching, etc.)
  - Each rule has: rule ID, condition, action, rationale

## 6. Assumptions & Risks
- **6.1 Implicit Assumptions** (magic numbers, hardcoded values, column dependencies)
- **6.2 Data Quality Risks** (what could silently go wrong)
- **6.3 Rebuild Checklist** (what to verify before reproducing)
```

### Task Subsection Template

Every task MUST follow this exact structure (no exceptions):

```markdown
### Task N: [task_name] — [one-line purpose]

[Mermaid flowchart — always TD direction]
```mermaid
flowchart TD
    subgraph 输入
        ...
    end
    subgraph 处理
        ...
    end
    subgraph 输出
        ...
    end
```

**Core Logic:**
| Step | Rule | Notes |
|------|------|-------|
| ... | ... | ... |

**Output path:** `data/output/[task]/...`

**Output format:** [brief description — single object / CSV / one-per-X file]
| Field | Type | Business meaning | Source |
|-------|------|-----------------|--------|
| ... | ... | ... | ... |

[JSON example or before/after table]
```

### Key Design Principles

1. **Self-contained tasks**: A reader should understand a single task by reading only its subsection — no jumping to section 2 for output schema.
2. **TD-only flowcharts**: All Mermaid diagrams use `flowchart TD` (top-down). Never use `flowchart LR` — TD is more readable in document width.
3. **Output schema co-located with logic**: The output format table sits INSIDE the task subsection, between the logic table and the next task. Not in a separate "Schema" section.
4. **Shared models in section 2**: Only truly cross-cutting data structures (TreeNode, DataFrame schema used by 3+ tasks) belong in section 2. Task-specific output fields stay in section 3.

## Translation Rules

Translate code to business language — never paste raw code blocks:
- `df.groupby(...).agg(...)` → "aggregate to [grain], computing [metrics]"
- `df.merge(...)` → "join [left] with [right] on [key]"
- `df[df[col] > x]` → "filter: keep rows where [condition]"
- `df.fillna(...)` → "missing [column]: [strategy]"
- `np.where(...)` / `np.select(...)` → "classify into [categories] based on [conditions]"
- `scipy.stats.*` → "statistical test: [method] for [purpose]"

## Key Practices

**Flag magic numbers**: Every hardcoded threshold/constant gets `[待确认: why this value?]`

**Mark uncertainties**: If logic is ambiguous, mark `[待确认]` and explain what's unclear — don't guess.

**Mermaid diagrams**: Always use `flowchart TD` (top-down, never LR). Add `%% business comment` annotations for non-technical readers.

**Before/after examples**: For major transforms, show 2-3 rows of sample input→output to make the rule concrete. For JSON-producing tasks, show a representative JSON snippet with field annotations.

**Self-contained output**: Each task's output format (schema table + example) MUST live inside that task's subsection. Never collect all output schemas into a separate upfront section — readers should not need to flip back and forth.
