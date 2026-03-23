# Career Coach Skills for Claude Code

> 7 AI-powered skills that turn your job search into a data-driven, measurable pipeline.

Most job seekers spray resumes and pray. This skill suite brings **product analytics thinking** to your career — track your funnel, find bottlenecks, and optimize every stage from application to offer.

## What You Can Do

### Find Jobs (with zero dead links)
```
You: "Find me remote ML engineer jobs that pay over $170K"

Claude: Searches 5+ sources, verifies every URL is live, excludes expired
        listings, and presents only confirmed-active jobs with fit scores.

        Result: 7 verified listings (excluded 8 dead links that other
        tools would have shown you)
```

### Track Your Pipeline Like a Sales Funnel
```
You: "I applied to Samsara through a referral from John"

Claude: ✅ Created app-003: Samsara — Sr. ML Engineer
        Source: referral (John)
        Status: applied
        Funnel event logged.

You: "Show funnel"

Claude: ═══════════════════════════════════════
        📊 Job Search Funnel
        ═══════════════════════════════════════
        Applied:       8  → Response Rate: 38%
        Phone Screen:  2  → Screen→Onsite: 50%
        Onsite:        1  → Onsite→Offer:  0%

        Source Breakdown:
          Cold Apply:  6 → 1 interview (17%)
          Referral:    2 → 2 interviews (100%)  ← 6x better!
        ═══════════════════════════════════════
```

### Get Weekly Analytics Reports
```
You: "Weekly report"

Claude: ## Week of Mar 17-23, 2026
        - New applications: 5
        - Responses: 2 (40% — above 15% benchmark ✅)
        - Your tailored resumes convert 50% vs 0% for generic ✅
        - ⚠️ 3 applications stale for 14+ days — follow up?
        - Biggest gap: All rejections at phone screen stage
          → Recommendation: Practice positioning with interview-coach
```

### Analyze Any Job Description
```
You: [pastes a JD URL]

Claude: Match Score: 78% — Tier 1
        ✅ Strong: Python 8yr, ML modeling, supply chain domain
        ⚠️ Gap: Kubernetes experience (they want it, you don't have it)
        💡 Strategy: Lead with your $37M anomaly detection story,
           address K8s gap with your Docker learning path
```

### Practice Interviews (Any Role)
```
You: "Let's practice for the Samsara interview"

Claude: [Loads your STAR stories, the Samsara JD, and your skills]

        Q: "Tell me about a time you built an ML system that
            drove measurable business impact."

        [You answer]

        Score: Structure 4/5 | Specificity 3/5 | Impact 5/5
        Feedback: "Lead with the $37M number in your first sentence
        — don't make them wait 2 minutes for the punchline."
```

### Tailor Your Resume
```
You: "Tailor my resume for this Stripe ML Engineer role"

Claude: [Reads base resume + JD, identifies keyword gaps]
        - Added "distributed systems" (mentioned 3x in JD)
        - Reordered bullets: anomaly detection first (matches their
          fraud detection focus)
        - Saved to: data/job-search/resumes/stripe-ml-engineer.md
```

### Journal Your Career Growth
```
You: "I had a win today — my classifier went to production"

Claude: 🎉 Nice! Let me capture this.
        → New STAR story extracted (story-009)
        → Updated skills/inventory.json: "Production ML" 5→7
        → Milestone logged: First ML model in production
```

---

## Quick Start (5 minutes)

### 1. Clone
```bash
git clone https://github.com/velodskat99/claude-career-coach.git
cd claude-career-coach
```

### 2. Configure
```bash
cp profile.template.yaml profile.yaml
```

Edit `profile.yaml` with your info — name, target roles, skills, TC target. This is the only config file. All 7 skills read from it.

### 3. Use
```bash
claude
```

That's it. Start talking naturally — the skills trigger automatically.

---

## The 7 Skills

| Skill | Triggers When You Say... | What It Does |
|-------|-------------------------|-------------|
| **job-search** | "find me jobs", "what's out there" | Searches multiple sources, **verifies every URL is live**, presents only confirmed-active listings with fit scores |
| **jd-analyzer** | "what about this role?", [paste a JD] | Scores fit (0-100%), identifies gaps, maps your STAR stories to requirements, suggests interview strategy |
| **resume-tailor** | "tailor my resume for this role" | Rewrites bullets for ATS keywords, reorders for relevance, creates role-specific version |
| **tracker** | "I applied to X", "show funnel" | Logs applications, tracks pipeline stages, computes conversion rates, flags stale applications for follow-up |
| **analytics** | "weekly report", "rejection analysis" | Generates reports with funnel metrics, rejection patterns, resume A/B testing, benchmark comparison |
| **interview-coach** | "let's practice", "quiz me" | Role-specific mock interviews (DS/SWE/PM/Analytics) with scoring, feedback, and progress tracking |
| **career-journal** | "I had a win today", "weekly reflection" | Captures career events, extracts STAR stories, tracks skill growth, logs milestones |

## The Quantified Approach

Instead of spray-and-pray, treat your job search as a **conversion funnel**:

```
Applications  →  Responses  →  Interviews  →  Offers
     100      →      15     →       6      →     2      (typical)

Your metrics vs industry benchmarks:
  Response rate:    You 38%  vs  Industry 15%    ✅ Great
  Time to response: You 5d   vs  Industry 5.6d   ✅ Fast
  Referral rate:    You 100% vs  Cold apply 17%   💡 Network more
```

The system tracks:
- **Conversion rates** at each funnel stage
- **Source analysis** — referral vs cold apply vs recruiter inbound
- **Resume A/B testing** — which version gets more callbacks
- **Rejection patterns** — at which stage, and why
- **Time analysis** — how long you're stuck at each stage
- **Industry benchmarks** — how you compare to [Huntr 2025 data](https://huntr.co/research/2025-annual-job-search-trends-report)

## Bookmarklet (Optional)

Quick-log applications from your browser. Add this as a bookmark:

**Name:** `Log Application`
**URL:**
```
javascript:void(navigator.clipboard.writeText(JSON.stringify({url:location.href,title:document.title,date:new Date().toISOString().slice(0,10)}))&&alert('Copied! Paste into Claude.'))
```

Click "Apply" on any career page → click bookmarklet → paste into Claude → auto-tracked.

## Data Architecture

```
profile.yaml                    # Your identity (single source of truth)
data/
├── job-search/
│   ├── tracker.json            # Pipeline + funnel events (event-sourced)
│   ├── resumes/base.md         # Master resume
│   └── weekly-summaries/       # Auto-generated pipeline snapshots
├── interview/
│   ├── stories.json            # STAR story bank
│   └── practice-log.json       # Interview practice scores + trends
├── analytics/
│   ├── benchmarks.json         # Industry benchmark data (Huntr 2025)
│   └── weekly-reports/         # Full analytics reports
└── sessions/                   # Coaching session summaries
```

All data is flat JSON + Markdown. No database, no server, no external dependencies. Your data stays local.

## Who Is This For?

- **Job seekers** who want to be systematic, not scattered
- **Career changers** who need to track a complex, multi-month search
- **Data people** who want to apply analytics thinking to their own career
- **Anyone using Claude Code** who wants an AI career coach that remembers everything

Works for any technical role — Data Scientist, Software Engineer, Product Manager, Analytics Engineer, or any custom role you define in `profile.yaml`.

## Built With

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — AI-powered CLI
- Pure Markdown skills (no code, no dependencies)
- JSON + YAML for data persistence
- Benchmarks from [Huntr 2025 Annual Report](https://huntr.co/research/2025-annual-job-search-trends-report)

## License

MIT
