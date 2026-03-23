---
name: analytics
description: "Job search analytics and insights engine. Generates weekly/monthly reports, analyzes rejection patterns, compares resume performance, tracks time-in-stage metrics, and benchmarks your search against industry data. Use this skill when the user asks for analytics, insights, reports, or says things like 'how am I doing', 'show me trends', 'rejection analysis', 'resume performance', 'benchmark my search', '報告', '分析', 'weekly report', 'monthly report'. Also triggers for 'what should I improve', 'where am I losing', 'which resume works best'."
---

# Analytics — Job Search Insights & Reports

You generate data-driven insights from the user's job search tracker. Your goal is to turn raw pipeline data into actionable recommendations — identify bottlenecks, spot patterns, and help the user optimize their search strategy.

## Before You Start

1. Read `profile.yaml` (fallback: `data/me.md`) for user identity and targets.
2. Read `data/job-search/tracker.json` — the raw pipeline data.
3. Read `data/analytics/benchmarks.json` for industry benchmark data (Huntr 2025).
4. Read recent reports from `data/analytics/weekly-reports/` if they exist (for trend comparison).
5. Read `data/strategy/goals.json` for TC targets and timeline.

## Core Functions

### 1. Weekly / Monthly Report

Generate a comprehensive report covering:

**Header:**
```
# Job Search Report — Week of {date range}
Generated: {today's date}
```

**Sections:**

**a) Period Activity**
- New applications added
- Applications submitted
- Responses received (positive + negative)
- Interviews conducted
- Offers received

**b) Pipeline Snapshot**
Current count per stage (visual bar chart using text):
```
Watching:     ████ 4
Preparing:    ██ 2
Applied:      ████████ 8
Phone Screen: ██ 2
Onsite:       █ 1
Offer:        0
Rejected:     ███ 3
```

**c) Funnel Conversion Rates**
```
Applied → Response:     38% (3/8)    [Benchmark: ~15%]
Response → Interview:   67% (2/3)    [Benchmark: ~40%]
Interview → Offer:       0% (0/1)    [Benchmark: ~20%]
Overall Yield:           0% (0/8)    [Benchmark: ~3%]
```
Use green/red indicators vs benchmarks:
- Above benchmark → ✅
- Below benchmark → ⚠️

**d) Source Analysis**
```
| Source         | Applied | Responded | Rate  | vs Benchmark |
|----------------|---------|-----------|-------|--------------|
| Cold Apply     | 6       | 1         | 17%   | ✅ Above 6%  |
| Referral       | 2       | 2         | 100%  | ✅ Strong    |
| Recruiter      | 0       | -         | -     |              |
```

**e) This Period's Wins & Losses**
- List positive events (interviews, offers, positive responses)
- List negative events (rejections, ghosted)
- Extract lessons from each

**f) Action Items**
- Follow-up reminders for stale applications
- Suggested next steps based on funnel analysis
- Recommendations for improving weak conversion stages

Save to: `data/analytics/weekly-reports/{YYYY-MM-DD}-weekly.md`
For monthly: `data/analytics/weekly-reports/{YYYY-MM}-monthly.md`

### 2. Rejection Pattern Analysis

When the user asks "why am I getting rejected" or "rejection analysis":

Aggregate rejection data from all applications with `status: "rejected"`:

**a) Stage Distribution**
```
Where rejections happen:
  After applying (no response):  5  (63%)  ← Resume/ATS issue
  After phone screen:            2  (25%)  ← Positioning issue
  After onsite:                  1  (12%)  ← Skills/fit issue
```

**b) Pattern Detection**
- By company tier: Are Tier 1 companies rejecting more than Tier 2?
- By source type: Are cold applies rejected more than referrals?
- By role type: Are certain role titles rejecting more?
- By timing: Are same-day rejections (ATS) vs delayed rejections different?

**c) Actionable Recommendations**
Based on where most rejections happen:
- **Mostly at resume stage** → "Your resume may not be passing ATS. Consider: more keyword matching, quantified achievements, tailoring per job."
- **Mostly at phone screen** → "Your positioning may need work. Practice your elevator pitch and 'why this role' narrative."
- **Mostly at onsite** → "Technical or cultural gaps. Review interview feedback, practice with the interview-coach skill."
- **Mostly ghosted** → "Follow up more aggressively. Industry data shows 44% of applications get no response."

### 3. Resume A/B Testing

Compare performance of different resume versions:

```
Resume Version Performance:
| Version                        | Sent | Responded | Rate  |
|--------------------------------|------|-----------|-------|
| base.md                       | 3    | 0         | 0%    |
| google-ml-tailored.md         | 2    | 1         | 50%   |
| supply-chain-focused.md       | 4    | 2         | 50%   |

💡 Insight: Tailored resumes convert 50% vs 0% for generic.
   The supply-chain-focused version is your best performer.
```

Requirements: At least 3 applications with `resumeVersion` set and at least 1 with a response.

### 4. Time-in-Stage Analysis

Calculate how long applications spend at each stage:

```
Time-in-Stage (median days):
  Preparing → Applied:     7 days   [You're slow to pull the trigger]
  Applied → First Response: 5 days  [Benchmark: 5.6 days — on track]
  Phone Screen → Onsite:    8 days  [Benchmark: ~7 days — slightly slow]
  Onsite → Decision:       12 days  [Benchmark: 12 days — normal]
  Total: Applied → Offer:   32 days [Benchmark: 44-83 days — fast!]
```

Flag outliers: applications stuck in a stage much longer than the median.

### 5. Benchmark Comparison

Compare the user's metrics to industry data (Huntr 2025 Annual Report):

```
╔══════════════════════════════════════════════════╗
║           Your Search vs Industry                ║
╠══════════════════════════════════════════════════╣
║ Metric              You     Industry   Status    ║
║ ─────────────────── ─────── ────────── ──────── ║
║ Response rate       38%     ~15%       ✅ Great  ║
║ Time to response    5.2d    5.6d       ✅ Fast   ║
║ Time to offer       N/A     44-83d     ⏳ TBD    ║
║ Apps/week           4       16         ⚠️ Low   ║
║ Resume customized   75%     ~30%       ✅ Smart  ║
╚══════════════════════════════════════════════════╝
```

**Industry benchmarks (Huntr 2025):**
- Median time to first offer: 57-83 days
- Average response rate: ~15% (6.5% for tailored, 4.3% generic)
- Application to first interview: ~5.6 days
- Interview to offer: ~12 days
- Typical application volume: ~16/week
- Most common successful path: 10-20 targeted applications
- Referral vs cold apply: referrals yield ~10x higher conversion

### 6. Trend Analysis

If 4+ weeks of data exist, analyze trajectory:

**Momentum Score:**
- Compare this period's metrics to previous period
- Compute: application volume trend, response rate trend, interview rate trend
- Assign: **Improving** ↗️ / **Stable** → / **Declining** ↘️

```
📈 Momentum: Improving ↗️

  Metric          Last 2 Weeks    Previous 2 Weeks   Trend
  Applications    8               4                   ↗️ +100%
  Response Rate   38%             25%                 ↗️ +13pts
  Interviews      2               0                   ↗️ New!

  Key Driver: Switched to referral-first strategy — 100% referral response rate
```

**If declining:**
- Flag which metric is dropping
- Correlate with changes (e.g., stopped tailoring resumes, fewer referrals)
- Suggest corrective actions

## Data Requirements

All analytics are computed from `data/job-search/tracker.json`:
- `applications[]` for current state
- `funnel_events[]` for time-series analysis
- Application `timeline[]` for detailed event history
- `source_type` for source analysis
- `resumeVersion` for resume A/B testing
- `rejection_stage` and `rejection_reason` for rejection analysis

**Minimum data for meaningful analytics:**
- Funnel metrics: 5+ applications
- Rejection analysis: 3+ rejections
- Resume A/B: 3+ applications with different resume versions
- Trend analysis: 4+ weeks of funnel_events
- If insufficient data: say so honestly and suggest what data is needed

## Language

Match the user's language from `profile.yaml → preferences.language`. Use the user's name from profile. Technical terms stay in English.
