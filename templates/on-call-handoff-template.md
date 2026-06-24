# On-Call Handoff Template

**Version:** 1.0
**Last updated:** [DATE]
**Rotation:** [TEAM_NAME] — [PRIMARY/SECONDARY]
**Handoff cadence:** [Weekly on Mondays at 10:00 AM / Every Tuesday and Thursday at 9:00 AM]

---

## What This Is

This is the document you fill out before handing off on-call to the next person. The goal is that the incoming on-call engineer can read this in 10 minutes and know everything they need to operate for the next rotation.

A bad handoff means the new person wastes their first hour digging through Slack and PagerDuty to figure out what's happening. A good handoff means they're fully oriented before the first alert fires.

**Golden rule:** If you wouldn't want to receive this handoff, don't send it. Be thorough. Be honest about what's broken and what you're worried about. The incoming person will figure it out anyway — better they hear it from you.

---

## Handoff Summary

| Field | Value |
|-------|-------|
| **Outgoing on-call** | [NAME] |
| **Incoming on-call** | [NAME] |
| **Rotation start** | [DATE] [TIME] [TIMEZONE] |
| **Rotation end** | [DATE] [TIME] [TIMEZONE] |
| **Handoff meeting** | [DATE] [TIME] — [Zoom/Meet link] |
| **Escalation contact** | [NAME] — [PHONE] — [SLACK] |
| **Week rating (1–5)** | [1 (everything broke) to 5 (quiet week)] |

### Week in One Sentence

[A single sentence that captures the theme of the week. "Quiet week, one SEV-2 for the search API on Wednesday, otherwise clean." or "Rough week — four incidents, including a SEV-1 database outage. We're still stabilizing."]

---

## Active Incidents

Any incident that is still open, still being monitored, or has follow-up work in progress. Include anything you were watching even if it didn't hit the alert threshold.

| # | Incident | SEV | Status | Opened | Owner | What the next person needs to know |
|---|----------|-----|--------|--------|-------|-------------------------------------|
| 1 | [Brief title] | [1/2/3] | Open / Monitoring / Resolved-follow-up | [DATE] | [NAME] | [What's left to do, what to watch] |
| 2 | [Brief title] | [1/2/3] | Open / Monitoring / Resolved-follow-up | [DATE] | [NAME] | [What's left to do, what to watch] |
| 3 | [Brief title] | [1/2/3] | Open / Monitoring / Resolved-follow-up | [DATE] | [NAME] | [What's left to do, what to watch] |

### Incident Details

For each active incident, provide enough context that the incoming person can answer questions about it without digging.

**Incident: [TITLE]**

- **What happened:** [2–3 sentences]
- **Current state:** [Is it fixed? Mitigated? Still happening intermittently?]
- **What we're watching:** [Which dashboards, which metrics, what threshold means "it's back"]
- **Open action items:** [Links to tickets]
- **Postmortem status:** [Scheduled / In draft / In review / Done — link]
- **Who to talk to:** [Person with the most context]

---

## Pending Changes Log

What changed this week, and what's changing soon. The incoming person needs to know what was touched recently and what deploys are planned — those are the things most likely to break.

### Changes This Week (Already Deployed)

| Date | Change | Who | What changed | Risk level | Link (PR / deploy) |
|------|--------|-----|-------------|------------|---------------------|
| [DATE] | [Brief description] | [NAME] | [Specific — "Changed DB pool size from 50 to 25" not "Updated DB config"] | Low / Med / High | [LINK] |
| [DATE] | [Brief description] | [NAME] | [Specific] | Low / Med / High | [LINK] |

### Changes Planned (Next Rotation)

What should the incoming person expect? Scheduled deploys, migrations, maintenance windows, dependency upgrades.

| Date | Change | Who | Risk level | Rollback plan ready? | Link |
|------|--------|-----|------------|----------------------|------|
| [DATE] | [Brief description] | [NAME] | Low / Med / High | Yes / No | [LINK] |
| [DATE] | [Brief description] | [NAME] | Low / Med / High | Yes / No | [LINK] |

### Known Risky States

Things that aren't broken right now but worry you. "The database primary is running at 78% CPU under normal load — a traffic spike could push it over." "We're one instance failure away from capacity issues on the worker tier."

| Concern | Why it's worrying | What to do if it gets worse | Ticket |
|---------|-------------------|----------------------------|--------|
| [Description] | [Context] | [Action] | [LINK] |
| [Description] | [Context] | [Action] | [LINK] |

---

## Alert Noise Review

On-call is sustainable only if the pager is sane. Every rotation, review what actually paged you and decide if those alerts should exist, be tuned, or be silenced.

### Alerts That Fired This Week

| Alert Name | Times Fired | Actionable? | Action Taken | Tuning Needed? |
|------------|-------------|-------------|--------------|----------------|
| [Name] | [Count] | Yes / No / Partially | [What happened] | Yes / No — [Ticket link] |
| [Name] | [Count] | Yes / No / Partially | [What happened] | Yes / No — [Ticket link] |

### Top Alert Offenders

If an alert fired more than 3 times and was never actionable, it's noise. Noise trains people to ignore the pager. File a ticket to tune or remove it.

| Alert Name | Count | Why it's noisy | Proposed fix |
|------------|-------|----------------|--------------|
| [Name] | [COUNT] | [E.g., "Fires on every deploy, but errors self-resolve within 60 seconds"] | [E.g., "Add 2-minute window before alerting"] |

### Currently Silenced / Muted Alerts

What's muted and why. The incoming person should know about muted alerts so they don't duplicate the silencing or, worse, unmute something that's still broken.

| Alert Name | Silenced Since | Reason | Expires / Review Date | Ticket |
|------------|---------------|--------|----------------------|--------|
| [Name] | [DATE] | [Reason — "Known issue with third-party API, being fixed in PR #1234"] | [DATE] | [LINK] |

### On-Call Experience Score

Rate the past week's on-call experience honestly. This is how we track burnout and alert hygiene over time.

| Question | Rating (1–5) |
|----------|-------------|
| How many times were you paged outside business hours? | [NUMBER] |
| Of those pages, how many were actionable? | [NUMBER] |
| Did any alert wake you up unnecessarily? | Yes / No |
| Overall noise level this week | 1 (Silent) – 5 (Constant noise) |
| Did you feel equipped to handle what came up? | 1 (Completely lost) – 5 (Everything was documented) |
| Burnout check: how are you feeling? | 1 (Fine) – 5 (I need a break) |

---

## Team Health Check

On-call is a team function, not an individual burden. This section tracks coverage, handoff quality, and whether anyone needs support.

### Coverage This Week

- [ ] Primary on-call was available for all pages (Yes / No — if no, explain: [EXPLANATION])
- [ ] Secondary on-call was available when needed (Yes / No)
- [ ] Any coverage gaps? Days/times with no one available? ([DETAILS])
- [ ] Any PTO or scheduling conflicts the incoming person should know about? ([DETAILS])

### Handoff Quality Self-Assessment

| Question | Yes / No / Notes |
|----------|-------------------|
| Did you receive a complete handoff from the previous on-call? | [Y/N] — [Notes] |
| Was there anything you discovered mid-week that should have been in the handoff? | [Y/N] — [What] |
| Is there anything you're leaving out of this handoff that you're unsure about including? | [Y/N] — [What] |

### Team Morale Check

Bullet points, honest. This is read by the EM and tech lead, not publicly shared.

- [How's the team doing? Stressed? Motivated? Overwhelmed?]
- [Any interpersonal friction that's affecting on-call?]
- [Anyone who seems like they need a break from on-call?]
- [Anything the team should celebrate? A good save? A smooth deploy? A nice customer comment?]

---

## Weekly Stats Summary

Quick numbers. These get compiled into a quarterly on-call health report.

| Metric | This Week | Last Week | Trend |
|--------|-----------|-----------|-------|
| Total incidents | [COUNT] | [COUNT] | ↑ / ↓ / → |
| SEV-1 incidents | [COUNT] | [COUNT] | ↑ / ↓ / → |
| SEV-2 incidents | [COUNT] | [COUNT] | ↑ / ↓ / → |
| Number of pages (total) | [COUNT] | [COUNT] | ↑ / ↓ / → |
| Number of pages (outside business hours) | [COUNT] | [COUNT] | ↑ / ↓ / → |
| Mean time to acknowledge (MTTA) | [MINUTES] | [MINUTES] | ↑ / ↓ / → |
| Mean time to resolve (MTTR) | [MINUTES] | [MINUTES] | ↑ / ↓ / → |
| Deploys this week | [COUNT] | [COUNT] | ↑ / ↓ / → |
| Rollbacks this week | [COUNT] | [COUNT] | ↑ / ↓ / → |

---

## Knowledge Transfer Checklist

Before you hand off, go through this list. If any item is incomplete, flag it for the incoming person.

### Documentation

- [ ] Runbooks: any updated? Any new ones created? (List: [LINKS])
- [ ] Known issues doc: updated with anything new? ([LINK])
- [ ] Architecture decisions: any new ADRs or tech specs written this week? ([LINKS])
- [ ] Postmortems: all SEV-1/SEV-2 postmortems from this week in draft or better? ([LINKS])

### Configuration & Secrets

- [ ] Any environment variables changed this week? (List: [CHANGES])
- [ ] Any new secrets added? Where? Who has access? ([DETAILS])
- [ ] Any feature flags toggled? Still toggled? ([LIST])

### Infrastructure

- [ ] Any infrastructure changes (new instances, scaling adjustments, network changes)? ([DETAILS])
- [ ] Any database migrations run? Pending? ([DETAILS])
- [ ] Any TLS certificates expiring within the next 14 days? (Check: [CERT_CHECK_URL])
- [ ] Any cloud provider maintenance windows upcoming? ([DATES])

### Monitoring & Alerting

- [ ] Any new alerts added? ([LIST])
- [ ] Any alerts removed or tuned? ([LIST])
- [ ] Any dashboards created or updated this week? ([LINKS])
- [ ] Is there an alert for everything that broke this week? (If something broke and didn't page, add an alert.)

### Access & Permissions

- [ ] Does the incoming on-call have access to everything they need? (AWS, Datadog, PagerDuty, GitHub, status page, third-party services)
  - [ ] Production infrastructure
  - [ ] Observability tools
  - [ ] Incident management (PagerDuty/Opsgenie)
  - [ ] Status page admin
  - [ ] Customer support tool (for checking user reports)
  - [ ] Third-party service status pages and admin consoles
- [ ] Any access issues this week? (E.g., "I couldn't access the Redis console and had to ask Alice to check it.")

---

## Action Items from This Rotation

Things that came up this week that the next person (or someone) needs to do. Not postmortem action items — those are tracked separately. This is the miscellaneous stuff.

| # | Action | Owner | Priority | Due | Notes |
|---|--------|-------|----------|-----|-------|
| 1 | [Description] | [NAME] | High / Med / Low | [DATE] | [Context] |
| 2 | [Description] | [NAME] | High / Med / Low | [DATE] | [Context] |

---

## Handoff Meeting Agenda (30 minutes)

Use this structure for the live handoff meeting between outgoing and incoming on-call. It's 30 minutes. Stay on track.

### 0:00–5:00 — Week Overview
- Outgoing: "Here's what happened this week in one minute."
- Incoming: "Here's what I'm worried about or want to know more about."

### 5:00–15:00 — Active Incidents Deep Dive
- Walk through each active incident in the tracker.
- Outgoing: "Here's what you need to watch and what I'd do if it flares up again."
- Incoming asks questions until they're comfortable.

### 15:00–20:00 — Pending Changes Review
- Review what was deployed this week and what's planned for next week.
- Flag any high-risk changes.
- Confirm rollback plans for upcoming deploys.

### 20:00–25:00 — Alert and Monitoring Review
- Walk through the alert noise review.
- Discuss any alerts that were problematic.
- Incoming: "Is there anything I should adjust in my notification settings?"

### 25:00–30:00 — Wrap-Up
- Review action items.
- Confirm the incoming person has all necessary access.
- Incoming: "I'm clear on everything. I'm ready to take over."
- Hand over the pager / update PagerDuty schedule / flip the on-call rotation.

---

## Fill-in-the-Blank: Quick Start

If you're the outgoing on-call and you're rushing, fill in at least these fields. The incoming person can ask about the rest.

**Week summary (one sentence):**
> [Write exactly one sentence.]

**Are there any active incidents right now?**
> [Yes — list them below / No, everything is stable.]

**What's the riskiest thing deployed this week?**
> [Description. If nothing, say "Nothing risky."]

**What's deploying next week that I should watch?**
> [Description. If nothing scheduled, say "Nothing scheduled."]

**Is there anything currently muted/silenced that I should know about?**
> [Yes — explain / No.]

**Are you burned out? (Seriously.)**
> [No, I'm fine. / A little tired but okay. / Yes, I need a break — here's why.]

**What's one thing you wish you'd known when you started this rotation?**
> [Tip for the incoming person.]

---

## Common Handoff Failure Modes

These are the ways handoffs go wrong. We've seen all of them. Read this before you write your handoff so you don't accidentally do one of these.

### Failure Mode 1: The Vague Wave

**What it looks like:** "Everything is fine. Quiet week. Some alerts, nothing major."

**Why it's bad:** The incoming person has zero actionable information. When something breaks at 2 AM, they have no idea what's been unstable, what was recently changed, or what to watch. They'll waste 45 minutes catching up on Slack.

**Fix:** Be specific. Even on quiet weeks, note what didn't happen. "No incidents this week. Three low-priority alerts — all auto-resolved. No deploys. The database primary is running at 65% CPU under normal load, which is higher than last week — worth watching." That's useful.

### Failure Mode 2: The Firehose

**What it looks like:** A 40-page document with every Slack thread, every alert, every thought the outgoing person had all week. The incoming person opens it, scrolls for 30 seconds, and closes it.

**Why it's bad:** The signal-to-noise ratio is zero. The incoming person can't find what matters.

**Fix:** The handoff is a highlight reel, not an exhaustive journal. If the incoming person needs more detail on something, they can ask. Put the critical stuff in the handoff, link to the rest.

### Failure Mode 3: The Whitewash

**What it looks like:** The outgoing person downplays or omits incidents because they don't want to look bad or because they're embarrassed about how they handled something.

**Why it's bad:** The incoming person gets blindsided. And you've just hidden information that could help them respond faster.

**Fix:** Be honest. "I mishandled the initial response to the SEV-2 on Tuesday — waited 20 minutes before declaring IC. Here's what I should have done. Here's the current state. Don't make my mistake." This is a professional handoff, not a performance review.

### Failure Mode 4: The No-Show

**What it looks like:** The outgoing person is burned out, or the rotation ended at a weird time, or there was no scheduled meeting — and the handoff doc is blank except for "sorry, busy week."

**Why it's bad:** The incoming person starts their rotation blind. If the outgoing person can't do a proper handoff, they need to say so explicitly and the EM or tech lead needs to fill the gap.

**Fix:** If you genuinely can't do a full handoff, fill out the Quick Start section (5 minutes) and tell the incoming person what's missing. Better: ask the EM or a teammate to help you complete it.

### Failure Mode 5: The Optimism Trap

**What it looks like:** "That intermittent error I've been seeing all week? Probably nothing. I'm sure it'll clear up."

**Why it's bad:** Intermittent issues don't clear up — they escalate. Usually at 3 AM on the incoming person's second day.

**Fix:** If something was weird but you didn't have time to investigate, flag it explicitly. "Noticed intermittent 504s on the search API — 3 occurrences this week, each lasting ~30 seconds. I couldn't reproduce reliably. I filed [TICKET] and added a note in #eng. It might be nothing, but here's what to check if it gets worse."

---

## Example Filled-Out Handoff

This is what a real handoff looks like. Not a blank template — an actual week. Use this as a reference for the level of detail and tone we're aiming for.

### Handoff Summary

| Field | Value |
|-------|-------|
| **Outgoing on-call** | Jordan |
| **Incoming on-call** | Casey |
| **Rotation start** | Monday, June 22, 2026 |
| **Rotation end** | Friday, June 26, 2026 |
| **Handoff meeting** | Friday, June 26, 4:00 PM — [Zoom link] |
| **Week rating** | 2 (below average — two incidents, both SEV-2) |

**Week in one sentence:** Two SEV-2s related to the new search infrastructure, plus elevated alert noise from a noisy CPU alert that we need to tune.

### Active Incidents

| # | Incident | SEV | Status | Opened | Owner | What Casey needs to know |
|---|----------|-----|--------|--------|-------|--------------------------|
| 1 | Search API latency spikes | 2 | Monitoring | June 24 | Jordan | Mitigated by scaling up search nodes. Latency is back to normal but we're watching for recurrence. If p95 crosses 500ms, scale up again using the runbook. Permanent fix (query optimization) is in PR #4521. |
| 2 | Staging environment flaky deploys | N/A | Open | June 22 | Casey (now) | Deploys to staging intermittently fail with a Docker network timeout. Workaround: retry. It's annoying but not blocking production. I opened #4524 to investigate. Low priority. |

### Changes This Week

| Date | Change | Who | What changed | Risk |
|------|--------|-----|-------------|------|
| June 22 | Deploy search v4.1 | Jordan | Migrated search indexing to new Elasticsearch cluster | High |
| June 23 | Config change | Jordan | Increased search node count from 3 to 8 to handle latency | High |
| June 24 | Hotfix | Priya | Fixed a typo in the billing email template — "your" vs "you're" | Low |

### Changes Planned (Next Week)

| Date | Change | Who | Risk | Notes |
|------|--------|-----|------|-------|
| June 29 | Database minor version upgrade (Postgres 15.3 → 15.4) | DevOps | Medium | Should be transparent — minor version. Tested in staging. Rollback is just a point-in-time restore if needed. |
| July 1 | New signup flow A/B test launch | Product eng | Medium | Wrapped in feature flag. Can disable instantly if anything breaks. |

### Known Risky States

| Concern | Why | What to do | Ticket |
|---------|-----|-----------|--------|
| Search node memory trending up | After scaling to 8 nodes, memory usage is at 72% and slowly climbing. Not urgent but could become an issue over the weekend. | If memory exceeds 85%, scale to 10 nodes. If it keeps climbing after that, page the search team (on-call list in #search-team). | #4526 |
| Database CPU creeping up | Primary DB CPU went from 45% to 58% over the week. Probably the new search queries, but worth watching. | If CPU exceeds 75%, check slow query log and consider killing long-running queries. Pager will fire at 80%. | #4527 |

### Alert Noise Review

**Alerts that fired:**

| Alert | Times | Actionable? | Notes |
|-------|-------|-------------|-------|
| Search API error rate > 5% | 2 | Yes | Both were real — triggered SEV-2s |
| DB CPU > 80% | 4 | No (all were false) | Alert threshold is too sensitive — 80% is our normal peak. Recommend raising to 85% |
| Worker queue depth > 1000 | 1 | Partially | Queue backed up briefly during search incident. Self-resolved. |

**Top offender:** DB CPU alert — fired 4 times, zero were actionable. I filed #4530 to raise the threshold to 85% and add a 5-minute window before alerting.

**On-Call Experience Score:**
- Paged outside business hours: 3 times
- Actionable pages: 2 of 3
- Woken up unnecessarily: Yes — the DB CPU alert at 3:12 AM Tuesday. Was at 81% for 30 seconds then dropped.
- Noise level: 3/5
- Felt equipped: 4/5 — runbooks covered the search issues well
- Burnout: 3/5 — a little tired but fine

### Action Items for Casey

| # | Action | Priority | Due | Notes |
|---|--------|----------|-----|-------|
| 1 | Monitor search API latency through the weekend | High | Ongoing | If it spikes again, scale nodes and page search team |
| 2 | Review and merge PR #4521 (search query optimization) | High | EOW | This is the permanent fix for the SEV-2s this week |
| 3 | Check if DB CPU alert threshold change (#4530) was deployed | Med | Monday | If not, you'll keep getting false pages |
| 4 | Investigate staging deploy flakiness (#4524) | Low | Whenever | Not urgent, but annoying |

### Jordan's Tips for Casey

- The search team is responsive on Slack — don't hesitate to pull them in if search latency acts up.
- The DB CPU alert will probably false-alarm until #4530 ships. I've been acknowledging and going back to sleep. You can silence it if it gets annoying, just let #eng know.
- Priya is out Monday-Tuesday next week, so if the billing code breaks, escalate to Alex.
- The office AC is broken on the 3rd floor. If you're working from the office, bring a fan.

---

## Tools and Links Reference

Bookmark these. The incoming person needs quick access to everything.

### Observability

| Tool | URL | What it's for | Credentials? |
|------|-----|---------------|-------------|
| Datadog | [URL] | Metrics, dashboards, APM | SSO |
| Grafana | [URL] | Infrastructure dashboards | SSO |
| Sentry | [URL] | Error tracking | SSO |
| CloudWatch / Stackdriver | [URL] | Cloud provider logs and metrics | [AWS/GCP console] |
| [LOG_AGGREGATOR] | [URL] | Centralized log search | SSO |

### Alerting & Incident Management

| Tool | URL | What it's for |
|------|-----|---------------|
| PagerDuty / Opsgenie | [URL] | Alerting, on-call schedules, escalation policies |
| Status Page | [URL] | Public status page admin |
| Incident Slack channel naming | `#incident-YYYY-MM-DD-short-description` | Where incidents are run |

### Infrastructure & Deploy

| Tool | URL | What it's for |
|------|-----|---------------|
| AWS / GCP / Azure Console | [URL] | Cloud infrastructure |
| Terraform / Pulumi repo | [URL] | Infrastructure as code |
| CI/CD pipeline | [URL] | Deploy dashboard, rollback controls |
| Docker / Container registry | [URL] | Container images |

### Team Resources

| Resource | URL | What it's for |
|----------|-----|---------------|
| Engineering handbook | [URL] | Team policies, architecture decisions |
| Runbook repo | [URL] | Service-specific runbooks |
| Postmortem archive | [URL] | All past postmortems |
| Team calendar | [URL] | PTO, deploy freezes, maintenance windows |
| Support ticket queue | [URL] | User-reported issues |

### Third-Party Status Pages

| Service | Status Page | What it affects if down |
|---------|-------------|------------------------|
| [SERVICE] | [URL] | [IMPACT] |
| [SERVICE] | [URL] | [IMPACT] |
| [SERVICE] | [URL] | [IMPACT] |

---

## Weekly Handoff Checklist (for the EM / Tech Lead)

If you're the engineering manager or tech lead, here's what you should check when you review the handoff each week. Don't just assume it was done — spot-check it.

- [ ] Handoff doc is complete (at minimum, the Quick Start section is filled out).
- [ ] Active incidents are documented with current status and owner.
- [ ] Alert noise review was done. Noisy alerts have tuning tickets filed.
- [ ] Team health check shows no red flags (severe burnout, coverage gaps).
- [ ] On-call experience score was recorded (track this over time — rising noise = failing alert hygiene).
- [ ] All SEV-1/SEV-2 incidents from the week have postmortems scheduled.
- [ ] Action items have owners and due dates — not just "someone should look at this."

---

## Appendix: On-Call Principles

Not part of the handoff, but worth re-reading. These are the principles we operate under.

1. **You are not alone.** If you're unsure, escalate. If you're overwhelmed, ask for help. On-call is a team responsibility, not a solo mission.

2. **The pager is not your enemy.** If it's noisy, fix it. Don't tolerate alert fatigue — it makes you worse at your job and miserable in your life.

3. **Document everything you learn.** If you figured out how to fix something that wasn't in a runbook, add it to the runbook. The next person shouldn't have to figure it out again.

4. **Handoff is sacred.** A bad handoff cascades into a bad week for the next person. Take the time to do it right.

5. **Take care of yourself.** If you were woken up at 3 AM, sleep in the next morning. If you had a rough on-call week, take a comp day. Burnout helps no one.

6. **Celebrate the wins.** When someone handles a tough incident well, say so. When the week is quiet, acknowledge that it's because the team did good work — not because "nothing happened."

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| [DATE] | Initial version | [NAME] |
| [DATE] | [Description of change] | [NAME] |
