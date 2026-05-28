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

## Career OS v2 Operating Rules

此 repo 的目的是確保使用者的職涯成長與求職成功，不只是保存筆記。所有 agent/skill 必須遵守以下資料流與決策規則。

### Single Source of Truth
- `profile.yaml` 是身份、目標、偏好、限制的唯一真相。
- `profile.yaml.role_strategy.excluded_tracks` 是硬限制。若 profile 排除 SWE-track roles，包含 Software Engineer、AI infrastructure SWE、embedded/C++ systems、frontend/backend product SWE，必須照做。
- 若使用者在對話中新增偏好或硬限制，必須更新 `profile.yaml`，不能只留在 chat/session summary。

### Job Search Data Flow
```
raw search results
  → data/job-search/raw-leads-{YYYY-MM-DD}.json
  → screen for fit / no-SWE / location / role shape
  → verify live listing + actual location + role shape
  → data/job-search/opportunities.json
  → only after the user chooses to pursue
  → data/job-search/tracker.json
```

- `data/job-search/raw-leads-*.json`：廣泛市場掃描池。可包含未驗證 LinkedIn / 104 / company career leads，但必須標記來源與狀態，不能當成推薦。
- `data/job-search/search-results-*.md`：研究過程與搜尋報告，必須列出 raw leads、screened candidates、verified opportunities、removed/excluded 的 counts。
- `data/job-search/opportunities.json`：verified/reviewed candidate pool。所有推薦職缺應先進這裡，並有 `verification.status`, `locationVerified`, `fit.score`, `decision.recommendation`。
- `data/job-search/tracker.json`：只放使用者已決定追蹤、準備、投遞、面試或 follow-up 的職缺。
- 不得把未驗證、地點不明、或只是 search snippet 的職缺放進 tracker。
- 嚴格 verified 定義：必須是 exact job page 或 rendered browser page 明確顯示 title、location、以及 apply/live signal。官方 search page、搜尋引擎 snippet、LinkedIn public snippet、JS 空頁、Workday 空頁只能標為 `search_page_only` 或 `needs_browser_verification`，不能當成已驗證推薦。
- 若 exact job page 或使用者截圖顯示 `No longer accepting applications`、`不再受理應徵`、expired、closed、not found，必須從 `opportunities.json` 移到 `removed`，即使搜尋結果仍顯示 `Apply`。
- LinkedIn 驗證優先使用 logged-in/rendered `linkedin.com/jobs/view/...` 畫面；`tw.linkedin.com/jobs/view/...` 的公開 crawler snippet 只能當候選線索，不足以判定職缺仍可投。

### Role Fit Rules
- 優先推薦：Applied AI Engineer、Senior Data Scientist、ML Application Engineer、Manufacturing Intelligence / Operations AI、AI Solutions Engineer。
- 可接受但需標記 tradeoff：Analytics Engineer、Decision Scientist、Technical AI Solutions / Cloud AI Advocate、NPI/Manufacturing Analytics with ML ownership。
- 預設排除：SWE-track、AI infrastructure SWE、embedded/C++ systems、pure PM、pure hardware、retail/ops-only。
- 台灣職缺必須確認實際工作地點，不可因 URL 是 `zh-tw` 或台灣版頁面就推定在台灣。
- 偏好外商，但台灣本土公司若與 semiconductor + applied AI fit 非常強，可作為 strategic exception。

## 職涯發展十大面向

```
Career Coach
├── 1. 自我認知 Identity       → data/me.md, career/
├── 2. 市場洞察 Market         → data/market/
├── 3. 技能發展 + 情報 Intelligence → data/intelligence/
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

### 3. 技能發展 + 情報 Intelligence (`data/intelligence/`)
- `intelligence/index.json` — 中央元數據（pending updates, staleness）
- `intelligence/feed/weekly/` — 自動週報（每週日產出）
- `intelligence/feed/insights/` — 手動/session 紀錄的洞察
- `intelligence/feed/sources.json` — 追蹤的情報來源
- `intelligence/skills/inventory.json` — 技能盤點（canonical skill inventory）
- `intelligence/curriculum/paths/` — 學習路徑（獨立檔案）
- `intelligence/curriculum/progress.json` — 學習進度追蹤

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
