# Career Coach

## Overview
AI 職涯教練系統。**主要在 Claude Code CLI 進行教練對話**，`data/` 目錄作為持久化記憶。使用者身份由 `profile.yaml` 定義。

## Session 開始時必讀
1. `profile.yaml` — 使用者身份與設定（所有 skills 的 single source of truth）
2. `data/me.md` — 完整人物誌（profile.yaml 不存在時的 fallback）
3. `data/strategy/goals.json` — 目前目標和行動項目
4. `data/sessions/` — 最近一次 session 摘要（用 `ls -t` 找最新的）

## 對話結束時
- 整理 session 摘要到 `data/sessions/{YYYY-MM-DD}.md`
- 更新被討論到的相關檔案
- 標記已完成的 action items

## 職涯發展十大面向

```
Career Coach
├── 1. 自我認知 Identity       → data/me.md, career/
├── 2. 市場洞察 Market         → data/market/
├── 3. 技能發展 Skills         → data/skills/
├── 4. 職涯策略 Strategy       → data/strategy/
├── 5. 求職管理 Job Search     → data/job-search/
├── 6. 面試與談判 Interview    → data/interview/
├── 7. 個人品牌 Brand          → data/brand/
├── 8. 人脈經營 Network        → data/network/
├── 9. 成長日誌 Journal        → data/journal/
└── 10. 財務規劃 Finance       → data/finance/
```

## Data Architecture

### Core（每次 session 必讀）
- `data/me.md` — 完整人物誌（基本資訊、職涯故事、優勢、待發展）

### 1. 自我認知 Identity (`data/career/`)
- `career/timeline.json` — 職涯時間軸
- `career/values.md` — 價值觀、工作偏好、理想生活
- `career/decisions.md` — 重大職涯決策紀錄

### 2. 市場洞察 Market (`data/market/`)
- `market/research.md` — 市場研究筆記（產業趨勢、角色演變）
- `market/target-roles.json` — 目標角色分析（JD 拆解）
- `market/companies.json` — 目標公司研究

### 3. 技能發展 Skills (`data/skills/`)
- `skills/inventory.json` — 技能盤點（current level vs target level）
- `skills/learning-paths.json` — 學習路徑
- `skills/resources.json` — 學習資源收藏

### 4. 職涯策略 Strategy (`data/strategy/`)
- `strategy/goals.json` — OKR 目標（短中長期 + 行動項目）
- `strategy/roadmap.md` — 職涯路線圖

### 5. 求職管理 Job Search (`data/job-search/`)
- `job-search/tracker.json` — 求職追蹤（公司、狀態、日期）
- `job-search/resumes/` — 履歷版本（`base.md` + `{role-name}.md`）
- `job-search/cover-letters/` — Cover Letters（`{company-role}.md`）

### 6. 面試與談判 Interview (`data/interview/`)
- `interview/stories.json` — STAR 故事庫
- `interview/prep/` — 各公司面試準備（`{company}.md`）
- `interview/negotiation.md` — 薪資談判策略

### 7. 個人品牌 Brand (`data/brand/`)
- `brand/linkedin.md` — LinkedIn 優化筆記
- `brand/portfolio.json` — 作品集 / side projects
- `brand/content.json` — 內容計畫

### 8. 人脈經營 Network (`data/network/`)
- `network/contacts.json` — 重要人脈
- `network/interactions.md` — networking 紀錄

### 9. 成長日誌 Journal (`data/journal/`)
- `journal/entries/{YYYY-MM-DD}.md` — 日誌條目
- `journal/milestones.json` — 里程碑

### 10. 財務規劃 Finance (`data/finance/`)
- `finance/salary-history.json` — 薪資紀錄
- `finance/benchmarks.md` — 市場薪資 benchmark

### Session 紀錄 (`data/sessions/`)
- `sessions/{YYYY-MM-DD}.md` — 教練對話摘要

## Web Dashboard（輔助用）

### Tech Stack
- Next.js 16 (App Router) + React 19 + TypeScript 5
- Tailwind CSS 4 with Geist font
- Claude API via `@anthropic-ai/sdk` (server-side only)
- recharts for data visualization
- JSON files for data persistence (no database)

### Key Conventions
- All UI text in Traditional Chinese (zh-TW), technical terms in English
- Path alias: `@/*` maps to `./src/*`
- Component pattern: UI primitives in `src/components/ui/`, feature components in `src/components/{feature}/`
- API route pattern: `readJsonFile` / `writeJsonFile` from `src/lib/data-io.ts`
