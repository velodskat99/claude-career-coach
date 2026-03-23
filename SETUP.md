# Setup Guide

Get your quantified job search system running in 5 minutes.

## Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed and authenticated

## Step 1: Clone

```bash
git clone https://github.com/YOUR_USERNAME/career-coach-skills.git
cd career-coach-skills
```

## Step 2: Configure Your Profile

```bash
cp profile.template.yaml profile.yaml
```

Open `profile.yaml` and fill in your information:
- Name, location, LinkedIn
- Target roles (Data Scientist, ML Engineer, SWE, etc.)
- TC target range
- Technical and domain skills
- Interview prep preferences (which role types to practice)

## Step 3: Set Up Data Directory

The `data/` directory stores your personal job search data. Create the basic structure:

```bash
mkdir -p data/job-search/resumes data/job-search/weekly-summaries
mkdir -p data/interview/sessions data/interview/prep
mkdir -p data/analytics/weekly-reports
mkdir -p data/sessions data/journal/entries
mkdir -p data/skills data/strategy data/career
```

Create your base resume:
```bash
touch data/job-search/resumes/base.md
# Edit base.md with your resume content in markdown
```

Create your STAR stories:
```bash
touch data/interview/stories.json
# See examples/stories.json for the format
```

## Step 4: Start Using

Open Claude Code in the project directory:

```bash
claude
```

Try these commands:
- **"Find me jobs"** — triggers job-search skill with URL verification
- **"I applied to Google"** — triggers tracker to log the application
- **"Show funnel"** — see your pipeline and conversion rates
- **"Let's practice interviews"** — starts a mock interview session
- **"Weekly report"** — generates analytics summary

## Bookmarklet (Optional)

For quick logging after applying on a website, add this as a browser bookmark:

**Name:** `Log Application`

**URL:**
```
javascript:void(navigator.clipboard.writeText(JSON.stringify({url:location.href,title:document.title,date:new Date().toISOString().slice(0,10)}))&&alert('Copied! Paste into Claude.'))
```

After clicking "Apply" on any career page:
1. Click the bookmarklet
2. Switch to Claude Code
3. Paste the JSON
4. Claude auto-creates a tracker entry

## Directory Structure

```
.
├── profile.yaml              # Your identity (fill this in!)
├── profile.template.yaml     # Blank template
├── CLAUDE.md                 # Instructions for Claude
├── .claude/skills/           # The 7 skills
│   ├── job-search/           # Find + verify job listings
│   ├── jd-analyzer/          # Analyze job descriptions vs your profile
│   ├── resume-tailor/        # Tailor resume per job
│   ├── tracker/              # Pipeline tracking + funnel analytics
│   ├── analytics/            # Reports + insights + benchmarks
│   ├── interview-coach/      # Mock interviews (any role type)
│   └── career-journal/       # Career reflection + growth logging
├── data/                     # Your personal data (gitignored)
│   ├── job-search/tracker.json
│   ├── interview/stories.json
│   └── ...
└── examples/                 # Example data files
```

## Customization

### Adding a New Interview Role Type

Edit `profile.yaml → interview_prep.target_roles` to add a new role:

```yaml
- id: "product-designer"
  label: "Product Designer"
  question_types: ["behavioral", "portfolio-review", "design-critique"]
  technical_topics: ["user-research", "interaction-design", "visual-design"]
  domain_scenarios: ["b2b-saas", "mobile-app"]
```

The interview-coach skill will automatically pick up the new role.

### Changing Your Target Industries

Edit `profile.yaml → industries` to change what domains the job-search and jd-analyzer skills focus on.
