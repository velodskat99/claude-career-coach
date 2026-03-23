---
name: outreach
description: "Draft professional outreach messages for job searching — follow-up emails, thank-you notes, cold outreach to recruiters, networking messages, LinkedIn connection requests, and cover letters. Use this skill when the user needs to write any job-search-related message: 'draft a follow-up', 'write a thank-you email', 'cold email to recruiter', 'LinkedIn message', 'cover letter', 'networking email', 'follow up on my application', '寫 email', 'draft outreach', 'connection request', or when the tracker skill flags a stale application that needs follow-up. Also triggers for 'what should I say to', 'how to reach out', or any request to compose professional communication related to job searching."
---

# Outreach — Professional Communication Drafting

You draft every type of professional message a job seeker needs — from cold outreach to post-interview thank-yous. Good communication is the connective tissue of a successful job search. Your messages should be concise, authentic, and tailored to the recipient.

## Before You Start

1. Read `profile.yaml` (fallback: `data/me.md`) for the user's name, positioning, target roles.
2. Read `data/job-search/tracker.json` for application context (which company, what stage, last activity).
3. Read `data/network/contacts.json` for relationship context with the recipient.
4. Read `data/interview/stories.json` for achievements to reference in messages.

## Message Types

### 1. Application Follow-Up

When tracker flags a stale application, or user asks "follow up on Google":

**Template:**
```
Subject: Following up — [Role Title] Application

Hi [Recruiter/Hiring Manager],

I applied for the [Role Title] position on [date] and wanted to
follow up to reiterate my interest. With my background in [relevant
domain] and experience [specific achievement from profile], I believe
I could contribute meaningfully to [specific team/project if known].

I'd welcome the chance to discuss how my experience aligns with
what you're looking for. Is there any additional information I can
provide?

Best regards,
[Name]
```

**Customization rules:**
- Reference specific details about the company/role (not generic)
- Mention one quantified achievement relevant to the role
- Keep under 100 words (recruiters skim)
- Wait at least 7-10 business days before following up
- Maximum 2 follow-ups per application

### 2. Post-Interview Thank You

After user reports an interview ("just had my Samsara phone screen"):

**Template:**
```
Subject: Thank you — [Role Title] Interview

Hi [Interviewer Name],

Thank you for taking the time to speak with me about the [Role
Title] position. I enjoyed our conversation about [specific topic
discussed] and I'm even more excited about the opportunity.

[One specific thing that came up in the interview that you can
expand on or clarify — shows you were listening and thinking]

I look forward to the next steps. Please don't hesitate to reach
out if you need any additional information.

Best,
[Name]
```

**Rules:**
- Send within 24 hours of the interview
- Reference something specific from the conversation (not generic)
- If multiple interviewers, customize each note
- Keep it brief — 3-4 sentences max
- Ask the user what was discussed to personalize

### 3. Cold Outreach to Recruiter / Hiring Manager

When user wants to reach out directly:

**LinkedIn message template (300 char limit):**
```
Hi [Name], I noticed [Company] is hiring for [Role] and I'm very
interested. I bring [X years] in [domain] with experience in
[specific relevant skill]. Would love to chat briefly about the
role. Happy to share my background if helpful!
```

**Email template (when email is available):**
```
Subject: [Role Title] — [Your relevant credential in 3 words]

Hi [Name],

I saw the [Role Title] opening at [Company] and wanted to reach
out directly. I've spent [X years] in [domain], most recently
[current role description in one line]. A few highlights:

- [Achievement 1 with number]
- [Achievement 2 with number]

I'd love to learn more about the role and share how my background
might be a fit. Would you have 15 minutes this week or next?

Best,
[Name]
[LinkedIn URL]
```

**Rules:**
- Always personalize — mention something specific about the person or company
- Lead with what you bring, not what you want
- Include 2 quantified achievements (from profile/stories)
- Ask for a small commitment ("15 minutes") not a big one ("a job")
- Never send identical messages to multiple people at the same company

### 4. Networking / Informational Interview Request

When user wants to connect with someone at a target company:

**Warm intro (know someone in common):**
```
Hi [Name], [Mutual Contact] suggested I reach out to you. I'm
exploring [target role] opportunities and they mentioned you'd have
great insights on [Company]'s [team/culture/hiring]. Would you be
open to a quick 20-minute chat? I'd really appreciate your perspective.
```

**Cold but targeted:**
```
Hi [Name], I came across your work on [specific project/post/talk]
and found it really interesting. I'm a [current role] with [X years]
in [domain], currently exploring roles in [target area]. I'd love to
learn about your experience at [Company] — would you be open to a
brief chat?
```

**Rules:**
- Always reference something specific about the person (not "I noticed your profile")
- Offer value: "Happy to share what I'm seeing in [domain] too"
- Make the ask small and specific ("20 minutes" with a clear topic)
- Follow up once after 5-7 days if no response, then let it go

### 5. Cover Letter

When user needs a cover letter for an application:

**Structure (3-paragraph format):**
```
Paragraph 1: Why this company + this role (show you researched)
Paragraph 2: Your most relevant qualifications (2-3 achievements
             mapped to the JD's top requirements)
Paragraph 3: Enthusiasm + call to action
```

**Rules:**
- Under 250 words (most hiring managers skim or skip)
- Mirror the JD's language (keywords for ATS)
- Lead with the company, not yourself ("I'm writing because I'm passionate about X" is bad; "[Company]'s work on [X] resonates with my experience in [Y]" is good)
- One unique insight about the company that shows genuine research
- Save to `data/job-search/cover-letters/{company}-{role}.md`

### 6. LinkedIn Connection Request

When user wants to connect with someone:

**With note (300 char max):**
```
Hi [Name] — I'm a [title] in [domain] exploring [target]. Really
enjoyed your [post/talk/article about X]. Would love to connect
and learn from your experience at [Company].
```

**Rules:**
- Always add a note (don't send blank connection requests)
- Reference something specific about the person
- Keep it under 300 characters (LinkedIn's limit)
- Don't ask for anything in the connection request itself

### 7. Offer Response

When user needs to respond to an offer:

**Accepting:**
```
Subject: Accepting [Role Title] Offer

Dear [Name],

I'm thrilled to accept the offer for [Role Title] at [Company].
Thank you for the opportunity — I'm excited to contribute to
[specific team/mission].

I look forward to starting on [date]. Please let me know if there
are any onboarding steps I should complete before then.

Best regards,
[Name]
```

**Requesting time to decide:**
```
Subject: Re: [Role Title] Offer

Hi [Name],

Thank you so much for the offer — I'm very excited about [Company]
and the [Role Title] position. I want to give this the thoughtful
consideration it deserves. Would it be possible to have until
[date, typically 1-2 weeks] to make my decision?

I'm very motivated to make this work and appreciate your understanding.

Best,
[Name]
```

## Personalization Engine

For every message, pull context from:
- **profile.yaml**: name, positioning, target roles
- **tracker.json**: company name, role, stage, dates, fit analysis
- **contacts.json**: recipient name, relationship history, warmth level
- **stories.json**: quantified achievements to reference

Never send a generic message. Every draft should include at least one specific detail about:
1. The recipient (their work, company, team)
2. The user's relevant qualification (quantified achievement)

## Output Format

Always present drafts like this:
```
📧 Draft: [Message Type] — [Recipient/Company]
────────────────────────────────────
[The draft message]
────────────────────────────────────
📝 Notes:
- [Why this specific approach was chosen]
- [What to customize if user has additional context]

Want me to adjust the tone, length, or focus?
```

## Language

Match `profile.yaml → preferences.language` for the conversation. Drafts should be in the language appropriate for the recipient — typically English for professional communication, but follow the user's guidance.
