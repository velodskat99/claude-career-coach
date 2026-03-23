# Career Coach Skills for Claude Code

> Quantified job search + career development, powered by Claude Code skills.

Turn your job search into a measurable, optimizable pipeline. Track your funnel, identify bottlenecks, and let AI handle the research while you focus on relationships.

## Features

| Skill | What It Does |
|-------|-------------|
| **job-search** | Find + verify job listings (0% dead links) |
| **jd-analyzer** | Analyze any JD against your profile (fit score, gaps, strategy) |
| **resume-tailor** | Tailor resume per job description |
| **tracker** | Pipeline tracking + funnel conversion analytics |
| **analytics** | Weekly reports, rejection analysis, resume A/B testing, benchmarks |
| **interview-coach** | Mock interviews for any role (DS, SWE, PM, Analytics) |
| **career-journal** | Career reflection + STAR story extraction |

## Quick Start (5 minutes)

### 1. Clone

```bash
git clone https://github.com/velodskat99/claude-career-coach.git
cd career-coach-skills
```

### 2. Configure

```bash
cp profile.template.yaml profile.yaml
# Edit profile.yaml with your info
```

### 3. Use

```bash
claude
```

Then just talk:
- **"Find me senior DS jobs in Austin"** — searches with URL verification
- **"I applied to Google"** — auto-logs to tracker with funnel event
- **"Show funnel"** — conversion rates, pipeline overview, follow-up alerts
- **"Weekly report"** — analytics with industry benchmark comparison
- **"Let's practice interviews"** — mock interview for your target role
- **"I had a win today"** — career journaling with STAR extraction

## The Quantified Job Search Approach

Instead of spray-and-pray, treat your job search as a **conversion funnel**:

```
Applications  →  Responses  →  Interviews  →  Offers
    投遞     →     回應     →     面試     →    Offer

Track conversion rate at each stage to find your bottleneck.
```

The **tracker** and **analytics** skills automatically compute:
- Response rate, interview rate, offer rate
- Referral vs cold apply conversion (referrals are typically 5-10x better)
- Resume version A/B testing (which resume gets more callbacks?)
- Rejection pattern analysis (where are you losing? ATS? Phone screen? Onsite?)
- Time-in-stage analysis (how long are you stuck at each stage?)
- Benchmark comparison vs [Huntr 2025 industry data](https://huntr.co/research/2025-annual-job-search-trends-report)

## Bookmarklet (Optional)

Quick-log applications without leaving your browser. Add this as a bookmark:

**Name:** `Log Application`
**URL:**
```
javascript:void(navigator.clipboard.writeText(JSON.stringify({url:location.href,title:document.title,date:new Date().toISOString().slice(0,10)}))&&alert('Copied! Paste into Claude.'))
```

**Usage:** Click "Apply" on career page → click bookmarklet → paste into Claude → auto-tracked.

## Interview Coach

Role-configurable mock interviews. Reads `profile.yaml` to determine question types:
- **Data Scientist**: ML, statistics, system design, case studies
- **Software Engineer**: coding, system design, API design
- **Analytics Engineer**: SQL, data modeling, pipeline design
- **Product Manager**: product sense, metrics, strategy

Uses your actual STAR stories for realistic behavioral practice with scoring and progress tracking.

## Data Architecture

```
profile.yaml                    # Your identity (single source of truth)
data/
├── job-search/
│   ├── tracker.json            # Pipeline + funnel events
│   ├── resumes/base.md         # Master resume
│   └── weekly-summaries/       # Auto-generated summaries
├── interview/
│   ├── stories.json            # STAR story bank
│   └── practice-log.json       # Interview practice history
├── analytics/
│   ├── benchmarks.json         # Industry benchmark data
│   └── weekly-reports/         # Analytics reports
└── sessions/                   # Coaching session summaries
```

All data is flat JSON + Markdown. No database, no server, no external dependencies.

## Built With

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — AI coding assistant
- Pure Markdown skills (no code dependencies)
- JSON + YAML for data persistence
- Industry benchmarks from [Huntr 2025 Report](https://huntr.co/research/2025-annual-job-search-trends-report)

## License

MIT
