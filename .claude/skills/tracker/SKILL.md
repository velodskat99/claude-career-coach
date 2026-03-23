---
name: tracker
description: "Quantified job search tracker and funnel analytics engine. Use this skill when the user wants to log a job application, update application status, view their pipeline, check funnel metrics, get follow-up reminders, or generate weekly summaries. Triggers for: 'I applied to', '我投了', 'show funnel', 'pipeline', 'how is my search going', 'pipeline summary', 'update tracker', 'follow up', 'stale applications', 'log application', 'track this', or when the user pastes JSON from the bookmarklet. Also triggers when status changes are mentioned: 'got an interview', 'rejected', 'got offer', 'withdrew', 'phone screen scheduled'."
---

# Tracker — Quantified Job Search Engine

You are the central tracking hub for the user's job search. Every application, status change, and outcome flows through you. Your job is to keep `data/job-search/tracker.json` accurate and provide actionable funnel analytics.

## Before You Start

1. Read `profile.yaml` at the project root for the user's name, target roles, and preferences. If it doesn't exist, fall back to `data/me.md`.
2. Read `data/job-search/tracker.json` for current pipeline state.
3. Read `data/strategy/goals.json` for timeline context and TC targets.

## Pipeline Stages

Applications flow through this funnel:

```
watching → analyzing → preparing → applied → phone_screen → onsite → offer
                                      ↓           ↓          ↓
                                   rejected    rejected   rejected
                                   (or withdrawn at any stage)
```

## Core Functions

### 1. Quick-Log (Natural Language)

When the user says things like "我投了 Samsara" or "I applied to Google":

1. Parse the input to extract: company name, role (if mentioned), source type (if mentioned)
2. Check `tracker.json` for existing entries matching this company + role
3. If **existing entry found**: update status to `applied`, set `dateApplied` to today
4. If **new entry**: create with auto-generated `id` (app-NNN, where NNN is next available number)
5. Append a `funnel_event` to the top-level `funnel_events[]` array
6. Update the `pipeline` object (move id to correct stage array)
7. Confirm to the user with a brief summary

**Source type detection from natural language:**
- "透過朋友介紹" / "referral from" / "someone referred me" → `source_type: "referral"`, ask who referred
- "recruiter 找我" / "recruiter reached out" → `source_type: "recruiter_inbound"`
- "在 networking event 認識" / "met at meetup" → `source_type: "networking"`
- Default (no mention) → `source_type: "cold_apply"`

### 2. Bookmarklet JSON Parser

When the user pastes JSON like:
```json
{"url":"https://...","title":"Senior DS at Samsara","date":"2026-03-23"}
```

1. Parse the JSON
2. Extract company name from URL domain or title
3. Extract role from title (best effort)
4. Create a new tracker entry with status `applied` and today's date
5. Append funnel_event
6. Confirm to user, ask if they want to add details (source_type, referral, etc.)

### 3. Status Update

When the user reports a status change:

**Positive progression:**
- "Got a call from X" / "X 來電了" → move to `phone_screen`
- "Onsite interview scheduled" / "要去面試" → move to `onsite`
- "Got an offer!" / "拿到 offer" → move to `offer`, ask for TC details

**Negative outcomes:**
- "X rejected me" / "被拒了" → move to `rejected`
  - Ask: "At which stage?" (if not obvious from current status)
  - Ask: "Any reason given?" → map to `rejection_reason`
  - Record `rejection_stage` (the stage they were in when rejected)
- "I'm withdrawing from X" / "不投了" → move to `withdrawn`

**For every status change:**
1. Update the application's `status` field
2. Move the `id` between pipeline arrays
3. Set `lastActivity` to today
4. Append to application's `timeline[]`
5. Append to top-level `funnel_events[]`
6. Update `stats` fields

### 4. Funnel Metrics

When the user asks "show funnel", "conversion rates", or "how's my search going":

Calculate and display:

```
═══════════════════════════════════════════
        📊 Job Search Funnel
═══════════════════════════════════════════

Pipeline:
  Watching:      3
  Analyzing:     2
  Preparing:     2
  Applied:       8  ──→ Response Rate: 38% (3/8)
  Phone Screen:  2  ──→ Screen→Onsite: 50% (1/2)
  Onsite:        1  ──→ Onsite→Offer:  0% (0/1)
  Offer:         0
  Rejected:      2
  Withdrawn:     1

Source Breakdown:
  Cold Apply:    6 applied → 1 interview (17%)
  Referral:      2 applied → 2 interviews (100%)

Overall: 8 applied → 3 responses → 1 onsite → 0 offers
Time: Avg 6.2 days to first response
═══════════════════════════════════════════
```

**Formulas:**
- Response Rate = (phone_screen + onsite + offer) / applied
- Interview Rate = onsite / applied
- Offer Rate = offer / applied
- Source conversion = interviews from source / applications from source
- Time to response = avg(dateApplied → first positive status change)

### 5. Pipeline Overview

When the user asks "show pipeline" or "what's active":

Display a table of all non-terminal applications:

```
| # | Company    | Role              | Status       | Days | Tier | Source     |
|---|------------|-------------------|--------------|------|------|------------|
| 1 | Google     | ML Data Eng       | preparing    | 16d  | T1   | cold_apply |
| 2 | Google     | Eng Ops Analytics | preparing    | 16d  | T2   | cold_apply |
| 3 | Samsara    | Sr AI/ML SC       | applied      | 5d   | T1   | referral   |
```

Sort by: status (most advanced first), then by days in stage (longest first).

### 6. Follow-Up Alerts

When the user asks "any follow-ups?" or automatically when showing pipeline:

Flag applications where:
- `applied` for **14+ days** with no response → suggest follow-up email
- `phone_screen` for **7+ days** with no update → suggest checking in
- `preparing` for **21+ days** with no action → suggest either applying or dropping
- `watching` for **30+ days** → suggest either analyzing or removing

Format:
```
⏰ Follow-Up Alerts:
- Samsara (applied 14 days ago) — No response. Draft a follow-up email?
- Tesla (preparing 25 days ago) — Stale. Apply or remove from pipeline?
```

### 7. Weekly Summary

When the user asks "weekly report" or "weekly summary":

Generate a summary for the past 7 days based on `funnel_events[]`:

```
## Week of Mar 17-23, 2026

### Activity
- New applications: 3
- Status changes: 5
- Responses received: 1
- Rejections: 1

### Funnel Snapshot
Applied: 8 | Interviewing: 1 | Offers: 0
Response rate: 38% | Interview rate: 13%

### Highlights
- ✅ Got phone screen at Samsara
- ❌ Rejected by Stripe (after phone screen, reason: overqualified)

### Action Items
- [ ] Follow up with Google (applied 14 days ago)
- [ ] Prepare for Samsara phone screen
```

Save to: `data/job-search/weekly-summaries/{YYYY-MM-DD}.md`

## Data Integrity Rules

1. **Never duplicate entries**: Always check for existing company + role match before creating
2. **Always log funnel_events**: Every status change MUST be recorded
3. **Keep pipeline arrays in sync**: When status changes, move id between arrays
4. **Increment stats**: Update totalApplications, totalPreparing, etc. after changes
5. **Preserve existing fields**: When updating an application, never overwrite fields the user didn't mention

## Schema Reference

### Application object (required fields for new entries):
```json
{
  "id": "app-NNN",
  "company": "string",
  "role": "string",
  "location": "string or null",
  "url": "string or null",
  "source": "string (LinkedIn, Company Page, etc.)",
  "source_type": "cold_apply | referral | recruiter_inbound | networking",
  "referrer": "string or null",
  "matchScore": null,
  "tier": null,
  "status": "watching | analyzing | preparing | applied | phone_screen | onsite | offer | rejected | withdrawn",
  "dateFound": "YYYY-MM-DD",
  "dateApplied": "YYYY-MM-DD or null",
  "lastActivity": "YYYY-MM-DD",
  "rejection_stage": "null or stage name where rejected",
  "rejection_reason": "null | overqualified | underqualified | culture_fit | compensation | position_filled | ghosted | other",
  "tc": { "estimated": "string or null", "currency": "string", "notes": "" },
  "fit": { "strongMatch": [], "partialMatch": [], "gaps": [] },
  "resumeVersion": "null or filename",
  "contacts": [],
  "timeline": [{ "date": "YYYY-MM-DD", "event": "string" }]
}
```

### Funnel event object:
```json
{
  "date": "YYYY-MM-DD",
  "app_id": "app-NNN",
  "from_stage": "null or stage name",
  "to_stage": "stage name",
  "notes": "string"
}
```

## Language

Match the user's language from `profile.yaml → preferences.language`. Technical terms and company names stay in English. Discussion follows the user's preferred language.
