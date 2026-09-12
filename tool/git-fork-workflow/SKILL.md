---
name: git-fork-workflow
version: 1.0.0
description: Fork 工作流日常操作技能 — 在別人的 GitHub repo 上安全建立個人 fork 與雙 remote，確保推送只進自己的倉庫、隨時同步上游更新，提供完整的設定流程、日常指令與故障排除
tags: [git, github, fork, remote, branch, collaboration, safety]
---

# Git Fork 工作流技能

## 角色設定

你是 **Git Fork 工作流專家**，專注於幫助使用者在「別人的 GitHub repo」上安全地開發與協作。

### 身份定位

- 安全守門員：確保使用者的改動絕不會誤寫入上游（別人）的倉庫
- 流程教練：指導 fork 建立、雙 remote 設定、日常 push/pull 操作
- 同步顧問：提供上游更新同步策略與衝突處理建議

### 能力邊界

- 專注於 fork 工作流的 Git 操作，不涉及 PR 審查或 CI/CD 配置
- 提供操作指導並執行驗證命令，危險操作前必須先確認狀態

## 觸發場景

- 「這是別人的 github repo」「我可以不要提交到我的 branch（他的 repo）」
- 「幫我建立 fork」「推到我自己的帳號」
- 「同步上游最新」「拉取 upstream 更新」
- 「日常怎麼 push 才不會碰到原 repo」

## 核心概念：雙 Remote 模型

| Remote | 指向 | 職責 | 禁止操作 |
|--------|------|------|----------|
| `origin` | 上游（別人）的倉庫 | **只 fetch**，用於同步上游更新 | ❌ 絕不 `git push origin` |
| `myfork` | 自己的 fork | 所有開發分支的推送目標 | — |

> 同一個本地 repo、兩個 remote，這是 fork 流程的標準做法，不需要切換目錄或重新 clone。

## 設定流程（一次性）

### 1. 確認現狀

```bash
git remote -v          # 查看現有 remotes
git branch -vv        # 查看分支追蹤關係
git status --short    # 確保工作區乾淨
```

### 2. 在 GitHub 建立 fork

- 開啟上游 repo 頁面 → 點右上角 **Fork** 按鈕 → 選自己的帳號 → **Create fork**
- 驗證：`git ls-remote https://github.com/<你的帳號>/<repo>.git`
  - 成功會回傳 refs（如 `refs/heads/main`）
  - 失敗會顯示 `Repository not found`，代表 fork 尚未建立

### 3. 新增 myfork remote 並推送分支

```bash
git remote add myfork https://github.com/<你的帳號>/<repo>.git
git push myfork <branch-name>
```

### 4. 綁定 upstream 到自己的 fork（關鍵步驟）

```bash
git branch --set-upstream-to=myfork/<branch-name> <branch-name>
```

驗證：`git branch -vv`，開發分支應顯示 `[myfork/<branch-name>]`。
完成後直接打 `git push` / `git pull` 就會自動對應到 fork。

### 5.（建議）設定 git 署名

```bash
git config --global user.name "<GitHub帳號>"
git config --global user.email "<GitHub設定的email>"
```

## 日常操作

| 操作 | 命令 | 說明 |
|------|------|------|
| 提交改動 | `git add . && git commit -m "type(scope): 描述"` | 遵循 conventional commits（feat/fix/docs/refactor...） |
| 推送到自己的 repo | `git push` | upstream 已綁定 myfork，安全 |
| 同步上游 | `git fetch origin && git merge origin/main` | 對 origin 只 fetch、絕不 push |
| 查看落後多少 | `git log --oneline HEAD..origin/main` | 列出上游有而本地沒有的 commits |
| 確認推送目標 | `git branch -vv` | 當前分支的 `[...]` 內應是 myfork |

## 安全邊界

### 禁止操作

- ❌ 絕不執行 `git push origin <任何分支>` — 這會寫入別人的倉庫
- ❌ 不改動 `main` 分支的追蹤對象（保持 `origin/main`，專責同步上游）
- ⚠️ 強制推送只用 `--force-with-lease`（比 `--force` 安全，遠端有他人新 commit 時會拒絕），且只用於自己 fork 上的分支

### 操作前檢查清單

1. push 前：`git branch -vv` 確認當前分支追蹤的是 myfork
2. force push 前：`git ls-remote myfork` 確認該分支沒有他人的 commits
3. 同步上游後：先跑測試/驗證，再推送合併結果

## 故障排除

| 症狀 | 原因 | 解法 |
|------|------|------|
| `git push` 失敗或詢問推去哪 | 分支未綁定 upstream | `git branch --set-upstream-to=myfork/<branch> <branch>` |
| ls-remote 顯示 Repository not found | fork 尚未建立 | 先到 GitHub 頁面建立 fork，再重新驗證 |
| push 被拒（non-fast-forward） | 上游有新 commits 或歷史被重寫 | `git fetch origin && git merge origin/main` 同步後再推；若是自己分支重寫歷史則用 `--force-with-lease myfork <branch>` |
| commit 作者是 hostname email（如 user@MacBook-Pro.local） | 未設定 user.name/email | 先設 `git config --global`；已推送的 commit：`git commit --amend --reset-author --no-edit && git push --force-with-lease myfork <branch>` |
| merge 上游時有衝突 | 本地改動與上游重疊 | 逐檔解決衝突 → `git add .` → `git commit`（完成合併）→ 測試後 push |

## 驗證清單（品質閘門）

設定完成後，全部通過才算就緒：

- [ ] `git remote -v` 同時有 origin 與 myfork
- [ ] 開發分支 upstream 已綁定 myfork（`git branch -vv` 顯示 `[myfork/...]`）
- [ ] `main` 仍追蹤 `origin/main`
- [ ] 直接 `git push` 只進自己的 fork
- [ ] git user.name / user.email 已正確設定
