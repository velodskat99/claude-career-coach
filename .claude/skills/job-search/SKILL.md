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

### Step 2: Collect Raw Candidate Listings

From search results, extract every potentially relevant job listing. For each, note:
- Company name
- Role title
- Location
- The URL you found

At this stage, include anything that looks plausible. You'll verify next.

Save this broad pool separately as `data/job-search/raw-leads-{YYYY-MM-DD}.json`. Do **not** collapse the market into only verified listings. A good Taiwan scan may have dozens of raw leads, but only a smaller number of verified opportunities.

Use these funnel stages:

- **raw_lead** — discovered from LinkedIn, 104, company career search, screenshot, or search result. Not yet trusted.
- **screened_candidate** — relevant to the user's profile after excluding obvious SWE, pure PM, wrong location, old/closed snippets, or low-fit roles.
- **verified_opportunity** — exact page confirms live apply signal and actual location.
- **removed** — exact page or screenshot confirms closed, no longer accepting, expired, wrong location, SWE-track, or otherwise not worth pursuing.

The final report must show counts for all stages. Do not make a small verified sample look like the whole market.

### Step 3: Verify Each Listing (CRITICAL)

This is the most important step. For EVERY listing you plan to present:

#### Verification Method Selection

Many company career sites are **JS-rendered** (Google Careers, TSMC, ASML, Dell, Oracle, etc.) and WebFetch will only return empty JavaScript bundles or 403 errors. You MUST detect this and escalate:

1. **First attempt**: Fetch the actual URL using WebFetch
2. **If WebFetch returns usable HTML** with job details → proceed with verification
3. **If WebFetch returns JS bundles, 403, or empty content** → the site is JS-rendered. You MUST use **Codex in Chrome (browser MCP)** to verify:
   - Navigate to the URL with `navigate`
   - Take a `screenshot` to visually confirm the listing
   - Look for "Job not found", "no longer available", "position filled", or redirect to generic search
   - Extract details from the rendered page using `get_page_text` or `read_page`
4. **If neither WebFetch nor browser works** → mark as "UNVERIFIED" and clearly label it. Do NOT present it as verified.

**NEVER trust search engine snippets or index results as verification.** Search engines cache listings for weeks after they are taken down. A listing appearing in Google search results does NOT mean it is live.

#### Failure Signals (check via WebFetch OR browser)
- HTTP 404 or other error codes → DEAD, exclude it
- Page says "this job is no longer available", "job not found", or "posting has expired" → DEAD, exclude it
- Page redirects to a generic careers page with no specific listing → DEAD, exclude it
- Page shows a different role than expected → WRONG, exclude it
- Career portal filter returns zero results for the role category → DEAD, exclude it

#### Known JS-Rendered Career Sites (always use browser)
- Google Careers (google.com/about/careers)
- TSMC (careers.tsmc.com)
- ASML (asml.com/en/careers)
- Dell (jobs.dell.com)
- Oracle (careers.oracle.com)
- Microsoft (careers.microsoft.com)
- Workday-based portals (*.wd3.myworkdayjobs.com, *.wd5.myworkdayjobs.com)
- Salesforce (careers.salesforce.com)

If you discover a new JS-rendered site during search, add it to this list.

#### Extract verified details from the live page:
- Exact job title (as shown on the page, not from search snippet)
- Location (as shown on the page)
- Key requirements (brief summary)
- Application URL or method
- Posted date if visible

**If you cannot verify a listing, do NOT include it as a verified opportunity.** Keep it in raw leads or screened candidates with a clear status. It's far better to present 5 verified jobs than 15 unverified recommendations, but it is also important to preserve the broader market view.

#### Verification Status Labels
Every listing MUST have one of these labels:
- **VERIFIED (browser)** — confirmed live via Codex in Chrome screenshot
- **VERIFIED (WebFetch)** — confirmed live via WebFetch with full job details returned
- **UNVERIFIED** — could not confirm; clearly label and warn the user
- **DEAD** — confirmed not available; exclude from results

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

Save results to `data/job-search/search-results-{YYYY-MM-DD}.md` with all URLs. Include a section at the top noting:
- How many listings were found in raw search
- How many became screened candidates
- How many were verified as live
- How many were removed or excluded, with reasons: 404, expired, closed, wrong location, SWE-track, irrelevant

Update:

- `data/job-search/raw-leads-{YYYY-MM-DD}.json` for the broad market pool
- `data/job-search/opportunities.json` only for verified / screened opportunities
- `data/job-search/search-results-{YYYY-MM-DD}.md` for the human-readable funnel report

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
