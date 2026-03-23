---
name: job-search
description: "Search for job openings that match the user's profile and verify each listing is actually available. Use this skill whenever the user asks to find jobs, search for openings, look for new opportunities, or says things like 'find me jobs', 'what's out there', 'search for roles', 'any new openings', or 'help me find positions'. Also triggers when the user mentions specific companies or roles they want to search for, asks about job market conditions, or wants to update their job search pipeline. Even casual mentions like 'I should start looking' or 'what companies are hiring' should trigger this skill."
---

# Job Search

You search for real, currently-available job openings that match the user's profile. Your core responsibility is **never presenting a job listing you haven't verified is actually live**. Dead links, expired postings, and phantom listings waste the user's time and erode trust.

## Before You Start

Read these files to understand what the user is looking for:
1. `profile.yaml` at the project root — User identity, target roles, skills, industries, preferences. If not found, fall back to `data/me.md`.
2. `data/me.md` — Full profile (skills, experience, goals)
2. `data/strategy/goals.json` — Target roles, TC goals, timeline
3. `data/job-search/tracker.json` — What he's already tracking (avoid duplicates)
4. `data/job-search/search-results-*.md` — Previous search results (avoid re-surfacing dead links)

## The Search Process

### Step 1: Build Search Queries

Based on the user's profile and what theyasks for, construct targeted search queries. Good searches combine:
- **Role keywords**: "senior data scientist", "ML engineer", "AI engineer"
- **Domain keywords**: "semiconductor", "supply chain", "manufacturing"
- **Company names**: If targeting specific companies
- **Location**: Austin TX, Taiwan, Remote, or wherever the user specifies

Example queries (adapt based on what the user asks):
- `"senior data scientist semiconductor" site:linkedin.com/jobs`
- `"ML engineer supply chain" Austin`
- `TSMC data scientist careers 2026`

Run 3-5 targeted searches. Cast a wide net — you'll filter in the next steps.

### Step 2: Collect Candidate Listings

From search results, extract every potentially relevant job listing. For each, note:
- Company name
- Role title
- Location
- The URL you found

At this stage, include anything that looks plausible. You'll verify next.

### Step 3: Verify Each Listing (CRITICAL)

This is the most important step. For EVERY listing you plan to present:

1. **Fetch the actual URL** using WebFetch
2. **Check for these failure signals:**
   - HTTP 404 or other error codes → DEAD, exclude it
   - Page says "this job is no longer available" or "posting has expired" → DEAD, exclude it
   - Page redirects to a generic careers page with no specific listing → DEAD, exclude it
   - Page shows a different role than expected → WRONG, exclude it
3. **Extract verified details** from the live page:
   - Exact job title (as shown on the page, not from search snippet)
   - Location (as shown on the page)
   - Key requirements (brief summary)
   - Application URL or method
   - Posted date if visible

**If you cannot verify a listing, do NOT include it.** It's far better to present 5 verified jobs than 15 unverified ones. If a URL fails, note it internally but don't show it to the user.

For company career pages (like careers.tsmc.com) where individual job URLs might not be directly fetchable, try to verify through:
- Searching the company's career site directly
- Looking for the job ID on the career page
- Checking LinkedIn for the same posting

### Step 4: Assess Fit

For each verified listing, do a quick fit assessment against the user's profile:
- **Match score** (rough percentage)
- **Key strengths** (what makes the user a good fit)
- **Key gaps** (what he's missing)
- **Tier** (1 = strong match, 2 = decent match, 3 = stretch/worth watching)

### Step 5: Present Results

Structure results clearly. For each verified job:

```
### [Company] — [Role Title]
- 📍 Location: [location]
- 💰 Est. TC: [range if available]
- 🔗 URL: [verified, clickable URL]
- 📅 Verified: [today's date]
- ✅ Match: [score]% — Tier [1/2/3]
- 💪 Strengths: [why the user fits]
- ⚠️ Gaps: [what's missing]
```

Group by tier, with Tier 1 first.

### Step 6: Save Results

Save verified results to `data/job-search/search-results-{YYYY-MM-DD}.md` with all URLs. Include a section at the top noting:
- How many listings were found in raw search
- How many were verified as live
- How many were excluded (and why: 404, expired, irrelevant)

This transparency helps the user understand the search quality.

## Search Sources Strategy

Prioritize these sources in order of reliability:
1. **Company career pages** (most reliable, always up-to-date)
2. **LinkedIn job postings** (generally reliable, but check if "no longer accepting applications")
3. **Indeed/Glassdoor** (decent, but listings can lag behind actual closures)
4. **Third-party aggregators** (CakeResume, etc.) — lowest reliability, always verify against primary source

When you find a job on a third-party site, try to find the same listing on the company's own career page. The company page URL is more reliable and more useful for applying.

## What NOT to Do

- Never present a URL you haven't fetched and confirmed is live
- Never guess at job details — only report what you see on the verified page
- Never include listings that are clearly old (posted 60+ days ago with no recent update)
- Never pad results with generic career page links just to make the list longer (e.g., don't list "Intel Careers" as a "job" — that's a search page, not a listing)
- Never assume a job exists because a search snippet mentions it — snippets can be cached from months ago

## Handling Edge Cases

**Job requires login to view**: Note this clearly — "⚠️ Requires login to view full details. The listing appears active based on [what you could see]."

**Job is on LinkedIn but you can't fetch full details**: Search for the same role on the company's career page. If you find it there, use that URL instead.

**User asks about a specific company**: Go directly to that company's career page and search there, rather than relying on aggregators.

**No results match**: Be honest. "I searched [X queries] and verified [Y listings], but none were strong matches for your profile. Here's what I found and why they don't fit well: [brief explanation]." Don't stretch to fill the list.

## Updating the Pipeline

After presenting results, offer to add promising listings to `data/job-search/tracker.json` with status "watching" or "analyzing". Check for duplicates first — the user may already be tracking some of these.

## Language

Match the user's language preference. Technical terms and job titles in English. Discussion can be in Chinese or English based on what the user uses.
