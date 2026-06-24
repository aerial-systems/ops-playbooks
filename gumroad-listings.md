# Gumroad Product Listings — Ops Playbooks

---

## Product 1: Incident Response Runbook

**Title:** The Incident Response Runbook

**Subtitle:** A complete, battle-tested process for handling production incidents — from first alert to blameless postmortem.

**Price:** $49

### Description

Most teams handle incidents by winging it. Someone gets paged, a few people jump into a Slack thread, and everyone does their best while the clock ticks and customers complain. It works until it doesn't — until a SEV-1 burns for three hours because nobody knew who was supposed to escalate, or a postmortem never happens because nobody owned it.

This runbook replaces guesswork with a process that's been refined across hundreds of real production incidents. It covers the full lifecycle: how to classify severity (and why you should always pick the higher level), who to escalate to and exactly when, minute-by-minute Incident Commander checklists, copy-paste communication templates for Slack, status pages, and customer emails, a blameless postmortem structure that actually produces action items instead of a document nobody reads, and five step-by-step runbooks for the incidents that happen most often — database outages, traffic spikes, TLS certificate expiry, bad deployments, and third-party dependency failures.

Everything is fill-in-the-blank. Put your team's names, phone numbers, dashboard URLs, and rollback commands in the marked slots, and you have a living document your on-call engineers can follow at 3 AM when they're half awake and something is on fire.

### What's Included

- **Severity classification framework** — SEV-1 through SEV-4 with clear triggers so there's no debate about how bad it is.
- **Escalation matrix** — who gets pulled in, when, and how. Includes a printable backup phone list for when Slack and PagerDuty are both down.
- **Incident Commander checklist** — minute-by-minute from acknowledge to resolution. Covers what to do at 0–5 minutes (declare, assemble, start the timer), 5–15 minutes (triage, blast radius, first status update), 15–30 minutes (delegate investigation, keep the timeline), and ongoing through resolution.
- **Five communication templates** — Slack initial declaration, Slack status update, Slack resolution, status page (initial/update/resolution), and a customer-facing email for outages lasting more than an hour.
- **Blameless postmortem template** — structured document with executive summary, timeline, what went well / what went wrong, Five Whys root cause analysis, action items with owners and due dates, and lessons learned.
- **Five detailed runbooks** — Database Outage, Traffic Spike / Overload, TLS Certificate Expiry, Bad Deployment, Third-Party Dependency Down. Each has symptoms, step-by-step commands (real bash/sql), and "need to know" reference sections.
- **Incident closing checklist** — everything that needs to happen before you go back to normal work (status page, postmortem scheduled, follow-up tickets filed, runbooks updated).
- **Quick reference card** — one-page printable summary for when you don't have time to read the whole document.

### Who This Is For

Engineering teams that have (or are building) an on-call rotation and want a real incident response process instead of "figure it out in Slack." Works for teams of 5 to 500. If you already have a mature incident management program with dedicated SREs and a full-time incident commander rotation, you probably have your own version of this. If you're still figuring out what SEV levels mean or your postmortems keep turning into blame sessions, this is for you.

---

## Product 2: On-Call Handoff Template

**Title:** The On-Call Handoff Template

**Subtitle:** A 10-minute handoff that actually tells the next person what they need to know — instead of leaving them to dig through Slack for an hour.

**Price:** $29

### Description

The worst part of on-call isn't the 3 AM pages. It's inheriting a rotation where the previous person left a Slack message that says "quiet week, nothing major" — and then you discover on Tuesday that there's been an intermittent database issue for six days, three alerts are silenced for reasons nobody documented, and a risky deploy went out Friday afternoon that nobody told you about.

This template makes bad handoffs structurally impossible. It walks the outgoing engineer through everything the incoming person needs: active incidents and what to watch, a log of what changed this week and what's deploying next, an alert noise review (which alerts are real and which are wasting everyone's sleep), a team health check that catches burnout before it becomes a resignation letter, and a 30-minute meeting agenda so the live handoff actually stays on track.

It also includes five common handoff failure modes — the Vague Wave, the Firehose, the Whitewash, the No-Show, and the Optimism Trap — with explanations of why each one screws over the next person and how to avoid doing it. Plus a filled-out example handoff from a real (anonymized) week so you can see what "good" actually looks like.

### What's Included

- **Handoff summary section** — one-page overview with week rating, one-sentence summary, and key contacts.
- **Active incidents tracker** — table format with status, owner, and exactly what the next person needs to watch. Includes a detail template for each incident.
- **Pending changes log** — what was deployed this week, what's deploying next week, and a "known risky states" section for things that aren't broken yet but worry you.
- **Alert noise review** — track which alerts fired, which were actionable, and which are noise that needs tuning. Includes an on-call experience score (pages outside hours, burnout check, felt-equipped rating).
- **Team health check** — coverage gaps, handoff quality self-assessment, and a morale check section (read by the EM, not shared publicly).
- **Weekly stats summary** — incident counts, MTTA/MTTR, deploy and rollback counts, with week-over-week trends.
- **Knowledge transfer checklist** — documentation, config/secrets, infrastructure, monitoring, and access/permissions. Did the incoming person get access to everything they need?
- **30-minute handoff meeting agenda** — timed sections with prompts for both outgoing and incoming engineers.
- **Quick start "fill-in-the-blank" section** — for when you're rushing and can only spend 5 minutes. Seven questions that cover the absolute minimum.
- **Five handoff failure modes** — with examples and fixes, so your team stops making the same mistakes.
- **Complete filled-out example** — a realistic week's handoff showing the level of detail and tone to aim for.
- **Tools and links reference** — bookmark section for observability, alerting, infrastructure, and team resources.
- **EM/tech lead weekly review checklist** — what managers should spot-check.

### Who This Is For

Any engineering team that runs an on-call rotation and hands off between engineers. If you've ever started an on-call shift and spent the first hour just trying to figure out what happened last week, this template pays for itself on the first use. Works for teams using PagerDuty, Opsgenie, or a shared Google Calendar and a prayer.

---

## Product 3: Deployment Checklist

**Title:** The Deployment Checklist

**Subtitle:** Ship to production without the sinking feeling. A 6-phase checklist that catches problems before your users do.

**Price:** $19

### Description**

"Just ship it" is a great motto until you take down production at 4:55 PM on a Friday. Every engineer has a bad deploy story. The common thread is almost never that someone was incompetent — it's that they skipped a step they didn't know they needed, or they deployed and walked away without watching the dashboards, or they didn't have a rollback plan because "it's a small change."

This checklist covers the full deploy lifecycle in six phases: pre-deploy verification (what are you shipping, is CI green, did you test at scale, is the rollback plan real or aspirational), the deployment itself (canary for high-risk, phased rollout for medium, direct for low — with exact percentages and monitoring windows), real-time monitoring of 13 specific metrics against a pre-deploy baseline, a rollback decision tree that tells you when to stop debugging and just revert, post-deploy validation at 15, 30, and 60 minutes (because some failures take time to surface), and a deploy log for correlating incidents later.

It also covers the edge cases: emergency deploys during active incidents (what you can skip and what you absolutely cannot), multi-phase database changes that aren't backward-compatible, and deployment freeze policies for holidays and high-traffic periods.

### What's Included

- **Deployment decision tree** — normal deploy, emergency hotfix, after-hours deploy, or freeze window? Each path has its own rules.
- **Phase 1: Pre-deploy verification** — seven sections covering what you're deploying, code review, testing (including load testing and edge cases), database migrations, config changes, communication, and capturing a dashboard baseline before you touch anything.
- **Phase 2: Deployment strategies** — canary deploy (1% → 5% → 25% → 100% with health checks at each stage), phased rollout (10% → 50% → 100%), and direct deploy. Includes automatic rollback trigger configuration.
- **Phase 3: Monitoring checklist** — 13-metric dashboard tracker (error rates, latency percentiles, CPU, memory, DB connections, queue depth, CDN cache ratio), plus log checks, alert checks, and manual smoke tests for core user flows.
- **Phase 4: Rollback decision tree** — when to roll back immediately vs. fix forward vs. investigate. Includes rollback commands (kubectl, ECS, git revert, feature flags), verification steps, and communication templates.
- **Phase 5: Post-deploy validation** — structured checks at 15, 30, and 60 minutes to catch slow failures (memory leaks, queue buildup, delayed cron failures).
- **Phase 6: Deploy log** — one-table record of every deploy for incident correlation and on-call handoff.
- **Emergency deploy procedure** — what you can skip during an incident and the five things you can never skip.
- **Multi-phase database change guide** — four-phase process for backward-incompatible schema changes, with rollback paths at each phase.
- **Deployment freeze policy** — template freeze windows, rules, and an exception log.
- **Quick reference card** — one-page printable summary covering pre-deploy, deploy, monitor, rollback, and post-deploy in a few lines.

### Who This Is For

Individual engineers who want a personal checklist before they hit deploy, and teams that want a shared standard. If your team's deploy process is "CI passes and we hope for the best," start here. If you've ever rolled back a deploy and realized the rollback procedure you thought existed was actually just a line in a README from 2019, this is for you.

---

## Product 4: Ops Starter Kit (Bundle)

**Title:** The Ops Starter Kit

**Subtitle:** All three production operations playbooks — incident response, on-call handoff, and deployment checklist — in one bundle. Everything a team needs to stop winging it.

**Price:** $79 (save $18 vs. buying individually)

### Description

Most engineering teams build their operations process the hard way: by making mistakes in production and then writing down what they should have done. This works, eventually, but it's slow and painful — and every lesson learned comes with a story that starts with "so we had an outage."

This bundle gives you the finished product of hundreds of those lessons, without having to live through them yourself. Three playbooks that work together: the Incident Response Runbook for when something breaks, the On-Call Handoff Template so the next person isn't starting blind, and the Deployment Checklist so fewer things break in the first place.

Each one is a fill-in-the-blank template. They're not generic advice — they have specific slots for your team's names, phone numbers, dashboard URLs, runbook commands, rollback procedures, and escalation paths. You customize them once, and your on-call engineers have a concrete process to follow instead of "figure it out and don't mess up."

The three playbooks are designed to reference each other. The incident runbook tells you to update the on-call handoff. The handoff template asks about recent deploys, which come from the deployment checklist's deploy log. The deployment checklist's rollback procedure feeds into the incident runbook's "Bad Deployment" runbook. They're separate documents, but they form a single operations workflow.

### What's Included

**The Incident Response Runbook ($49 value)**
- Severity classification (SEV-1 through SEV-4) with clear triggers and response expectations
- Escalation matrix with role-specific timelines and a printable backup phone list
- Incident Commander minute-by-minute checklist from acknowledge through resolution
- Five copy-paste communication templates (Slack, status page, customer email)
- Blameless postmortem template with Five Whys root cause analysis
- Five step-by-step runbooks for common incidents (database, traffic, TLS, bad deploy, third-party)
- Incident closing checklist and quick reference card

**The On-Call Handoff Template ($29 value)**
- Handoff summary and active incidents tracker
- Pending changes log and known risky states
- Alert noise review with on-call experience scoring
- Team health check, coverage assessment, and morale check
- Knowledge transfer checklist across documentation, config, infrastructure, and access
- 30-minute handoff meeting agenda
- Five common handoff failure modes with examples and fixes
- Complete filled-out example from a real week
- Quick start section for 5-minute handoffs
- EM/tech lead review checklist

**The Deployment Checklist ($19 value)**
- Six-phase deploy lifecycle from pre-deploy verification through post-deploy validation
- Three deployment strategies (canary, phased, direct) with exact percentages and monitoring windows
- 13-metric monitoring tracker with pre-deploy baseline comparison
- Rollback decision tree with commands for Kubernetes, ECS, and git-based rollbacks
- Post-deploy validation at 15, 30, and 60 minutes
- Emergency deploy procedure for active incidents
- Multi-phase database change guide
- Deployment freeze policy template

### Who This Is For

Teams that are serious about production operations but don't have months to build their processes from scratch. If you're growing fast and your current ops process is a mix of tribal knowledge, a few Slack channels, and hope — this bundle gets you to a documented, repeatable, teachable process in an afternoon. Also works well for engineering managers who just inherited a team and want to establish baseline practices without starting from a blank page.

---

*All products are digital downloads (Markdown files). Pay once, use forever. Customize for your team, your stack, and your escalation paths. No subscriptions, no platforms, no vendor lock-in.*
