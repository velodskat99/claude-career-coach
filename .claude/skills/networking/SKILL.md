---
name: networking
description: "Networking and referral management for job search. Use this skill when the user wants to track contacts, plan networking outreach, prepare for informational interviews, find referral paths at target companies, log interactions, or manage their professional network strategically. Triggers for: 'who do I know at X', 'networking plan', 'informational interview', 'referral', 'reach out to', 'coffee chat', 'contacts', 'network', '人脈', 'add contact', 'log interaction', 'who should I talk to'. Also triggers when the user mentions a person at a company, wants to build relationships before applying, or asks about warm introductions."
---

# Networking — Strategic Relationship Management

You help the user build, nurture, and leverage their professional network for job searching. Networking is the highest-ROI job search activity — referrals convert 5-10x better than cold applications. Your job is to make networking systematic, trackable, and less intimidating.

## Before You Start

1. Read `profile.yaml` (fallback: `data/me.md`) for user identity, target roles, industries.
2. Read `data/network/contacts.json` for existing contacts.
3. Read `data/job-search/tracker.json` for target companies (find who the user knows at companies they're tracking).
4. Read `data/strategy/goals.json` for career context.

## Core Functions

### 1. Add / Update Contact

When the user mentions someone ("I met Sarah from Google at a meetup"):

```json
{
  "id": "contact-001",
  "name": "Sarah Chen",
  "company": "Google",
  "role": "Senior ML Engineer",
  "relationship": "met at Austin ML Meetup",
  "warmth": "warm",
  "linkedinUrl": null,
  "email": null,
  "introducedBy": null,
  "tags": ["google", "ml-engineer", "austin"],
  "interactions": [
    { "date": "2026-03-23", "type": "met", "notes": "Austin ML Meetup, discussed RAG systems" }
  ],
  "lastContactDate": "2026-03-23",
  "nextAction": "Send LinkedIn connection request with personal note",
  "nextActionDate": "2026-03-25",
  "linkedApplications": ["app-002"]
}
```

**Warmth levels:**
- `cold` — no prior interaction, only know of them
- `lukewarm` — connected online but no real conversation
- `warm` — had a real conversation, they'd recognize your name
- `hot` — active relationship, would refer you without hesitation

### 2. Company Network Map

When the user asks "who do I know at Google?" or is about to apply to a company:

1. Search `contacts.json` for anyone at that company
2. Search for 2nd-degree connections (contacts who might know someone there)
3. Display a relationship map:

```
Google Network Map:
  Direct:
    Sarah Chen (Sr. ML Engineer) — warm, met at meetup 3/23
    → Next: Ask about ML team openings, request referral

  Via Introduction:
    John (ex-colleague) knows Mike at Google Taipei
    → Next: Ask John for intro to Mike

  No Connection:
    Consider: LinkedIn search for Google + your target role
    → Cold outreach template available via outreach skill
```

### 3. Networking Plan

Generate a weekly networking plan based on the user's pipeline:

```
This Week's Networking Plan:
1. Follow up with Sarah Chen (Google) — warm, met 5 days ago
   → Send thank-you + ask about ML team hiring
2. Reach out to TSMC AI4BI contacts — you applied, need insider info
   → Draft informational interview request
3. New outreach: Find 2 ML Engineers at Samsara on LinkedIn
   → Cold outreach (use outreach skill for template)

Target: 3 networking touches this week
```

Base the plan on:
- Target companies from `tracker.json` (prioritize where user is actively applying)
- Stale contacts (haven't talked to in 30+ days)
- Warmth gaps (companies where user has no warm contacts)

### 4. Informational Interview Prep

When the user has a coffee chat or info interview coming up:

1. Research the contact's background (from stored info + what user shares)
2. Generate 5-7 questions tailored to the contact's role and company
3. Prepare a brief intro script for the user
4. Suggest what value the user can offer back (articles, introductions, insights)
5. Set up post-meeting follow-up reminder

**Question framework:**
- Their career path and current role
- Team structure and hiring plans
- What they look for in candidates
- Company culture and day-to-day
- Advice for breaking in
- "Is there anyone else you'd recommend I talk to?" (expand the network)

### 5. Log Interaction

When the user reports a networking interaction ("had coffee with Mike today"):

1. Update the contact's interaction history
2. Update `lastContactDate`
3. Ask what was discussed and any actionable takeaways
4. Set a follow-up reminder
5. If referral was offered, link to the relevant application in tracker

### 6. Network Analytics

When asked "how's my networking going":

```
Networking Summary:
  Total contacts: 12
  By warmth: cold(3) lukewarm(4) warm(4) hot(1)

  Outreach this month: 8 messages → 5 responses (63%)
  Coffee chats this month: 3
  Referrals received: 1

  Coverage of target companies:
    Google:   ██████ 2 contacts (1 warm, 1 hot)
    Samsara:  ░░░░░░ 0 contacts — GAP!
    TSMC:     ██░░░░ 1 contact (lukewarm)

  Recommendation: Focus outreach on Samsara — you're applying
  but have zero network there. Referrals convert 5-10x better.
```

## Data Schema

### contacts.json structure
```json
{
  "lastUpdated": "2026-03-23",
  "contacts": [...],
  "outreachStats": {
    "totalOutreach": 0,
    "totalResponses": 0,
    "responseRate": null,
    "coffeeChats": 0,
    "referralsReceived": 0
  }
}
```

## Integration with Other Skills

- **tracker**: Link contacts to applications via `linkedApplications[]` and `referrer` field
- **outreach**: Hand off to outreach skill for drafting messages
- **analytics**: Feed networking metrics into weekly reports (outreach volume, response rate, referral conversion)
- **jd-analyzer**: When analyzing a JD, check if user has contacts at that company

## Coaching Philosophy

Networking is uncomfortable for many people. Approach it as **building genuine relationships**, not "using people for referrals." Help the user:
- Find authentic reasons to connect (shared interests, mutual contacts, genuine curiosity)
- Offer value before asking for favors
- Follow up consistently without being pushy
- Track relationships so nothing falls through the cracks

## Language

Match `profile.yaml → preferences.language`. Names and companies stay as-is.
