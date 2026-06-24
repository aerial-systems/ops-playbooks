# Deployment Checklist

**Version:** 1.0
**Last updated:** [DATE]
**Owner:** [TEAM_NAME]
**Applies to:** [SERVICE_NAME / ALL_PRODUCTION_SERVICES]

---

## What This Is

This is the checklist every engineer runs before, during, and after deploying to production. It covers the full lifecycle: pre-deploy verification, the deployment itself (canary, phased rollout, or full send), real-time monitoring, rollback, and post-deploy validation.

This checklist exists because "just ship it" sounds great until you take down production at 4:55 PM on a Friday. Following the checklist takes 5–10 minutes. Recovering from a bad deploy takes hours and costs trust.

**The rule:** If any pre-deploy check fails, don't deploy. Fix the issue and restart the checklist from the top. If any monitoring check fails mid-deploy, pause and investigate. If you're not sure whether to roll back, roll back.

---

## Deployment Decision Tree

Before you start, figure out what kind of deploy this is. The process is different depending on risk.

```
Is this a normal deploy during business hours?
├─ Yes → Follow this checklist. Canary → phased → full.
│
├─ No → Is this an emergency fix for an active incident?
│   ├─ Yes → See "Emergency Deploy Procedure" at the bottom.
│   │        You can skip some checks, but you must document what you skipped and why.
│   │
│   └─ No → Is it after hours but not an emergency?
│       └─ Yes → Wait until business hours unless you have explicit approval
│                from [EM_OR_TECH_LEAD_NAME]. No approval = no deploy.
│
└─ Is this during a deployment freeze?
    ├─ Yes → See "Deployment Freeze Policy." You need VP Eng / CTO approval.
    └─ No → Proceed.
```

**Deployment freeze windows:**
- [LIST YOUR FREEZE WINDOWS — e.g., "Last two weeks of December," "Black Friday week," "Week before major customer conference"]
- Current freeze status: [IN EFFECT / NOT IN EFFECT]
- Freeze exception approver: [NAME] — [CONTACT]

---

## Phase 1: Pre-Deploy Verification

Complete this phase before touching production. Every box must be checked.

### 1.1 What Are You Deploying?

- [ ] **Deploy description:** [One sentence describing what's being deployed and why.]
- [ ] **Service(s) affected:** [List every service that's changing.]
- [ ] **Change type:** Code deploy / Config change / Infrastructure change / Database migration / Feature flag toggle / Dependency upgrade / Hotfix
- [ ] **Risk level:** Low (cosmetic, non-critical path) / Medium (feature change, isolated) / High (touches auth, payments, database, infrastructure, or core user flows)
- [ ] **Rollback plan confirmed:** What's the rollback procedure? How long will it take? Who knows how to do it if you're not available? Is the rollback path tested?
  - Rollback procedure: [Describe in 1–3 sentences.]
  - Rollback time estimate: [MINUTES]
  - Backup rollback person: [NAME]

### 1.2 Code & Review

- [ ] **PR approved** by at least one other engineer. (Required for any production change.)
- [ ] **No outstanding review comments** that haven't been addressed. "I'll fix that in a follow-up" doesn't count for high-risk changes.
- [ ] **CI pipeline is green.** All tests pass. All linting passes. Build succeeds.
- [ ] **Merge conflicts resolved.** You're deploying from the main branch (or a known release branch), not from a stale feature branch.
- [ ] **Dependencies reviewed.** If you're upgrading a library or framework, did you check the changelog for breaking changes? Are all dependencies at compatible versions?

### 1.3 Testing

- [ ] **Tests pass locally.** You ran the test suite for the affected service(s) on your machine and it's green.
- [ ] **Manual smoke test done** in staging or a pre-production environment. You clicked through the affected flows and they work.
- [ ] **Load / performance test done** — required for any change that could affect performance (new queries, new API calls, config changes to pools/limits/threads). If you changed a resource limit or added a database query, you need to load test it.
- [ ] **Edge cases considered.** What happens if the database is slow? If a third-party API times out? If the user input is garbage? Have you tested these?
- [ ] **Feature flag in place?** For high-risk changes, can you wrap the new code in a feature flag so you can disable it without a full rollback?

### 1.4 Database Migrations

If this deploy includes a database change, answer these additional questions:

- [ ] **Migration is backward-compatible.** Can the old version of the code run against the new schema? (If not, this is a multi-phase deploy — see "Multi-Phase Database Changes" below.)
- [ ] **Migration tested against a production-sized dataset.** Running a migration on a dev database with 100 rows is not the same as running it on production with 50 million rows. Have you tested it at scale?
- [ ] **No locks that will block production traffic.** Does this migration take an `ACCESS EXCLUSIVE` lock? If so, have you confirmed it will complete in milliseconds, and have you tested it under production load?
- [ ] **Rollback migration is ready.** If this deploy goes bad, there's a tested migration to undo the schema change. (You wrote one, right?)
- [ ] **Migration run separately from code deploy?** For risky migrations, run the migration first (backward-compatible), verify it, then deploy the code. Don't do both at once.

### 1.5 Configuration Changes

- [ ] **Config validated.** If you're changing environment variables, YAML configs, or feature flags, the new values have been reviewed by a second person. No typos, no wrong units, no accidentally unsetting something.
- [ ] **Secrets handled correctly.** New secrets are in the secrets manager (not in the config file). Existing secrets weren't accidentally exposed in the PR.
- [ ] **Config change is backward-compatible.** Can the old code run with the new config? (Usually yes, but check.)

### 1.6 Communication

- [ ] **Team notified.** Post in the team Slack channel: "Deploying [SERVICE] to production in [X] minutes. [One-line description of change]. Risk: [Low/Med/High]. Rollback plan: [brief]."
- [ ] **Stakeholders notified (if high-risk).** PM, Customer Success, Support — anyone who'll field questions if something goes wrong should know a deploy is happening.
- [ ] **Deploy window confirmed.** Are you deploying during a known quiet period? Not during peak traffic? Not when another team is also deploying something risky?

### 1.7 Pre-Deploy Dashboard Baseline

**Take a screenshot or note the current state of these dashboards BEFORE you deploy.** You need to know what "normal" looks like so you can spot the deploy's impact.

- [ ] **Error rate:** [CURRENT_VALUE] — [DASHBOARD_URL]
- [ ] **Latency (p50/p95/p99):** [CURRENT_VALUES] — [DASHBOARD_URL]
- [ ] **Throughput (requests/second):** [CURRENT_VALUE] — [DASHBOARD_URL]
- [ ] **CPU / memory (per service):** [CURRENT_VALUES] — [DASHBOARD_URL]
- [ ] **Database connections / CPU:** [CURRENT_VALUES] — [DASHBOARD_URL]
- [ ] **Queue depth (background jobs):** [CURRENT_VALUE] — [DASHBOARD_URL]
- [ ] **Third-party integration health:** [CURRENT_STATUS] — [DASHBOARD_URL]

**Pre-deploy screenshot saved:** [FILE_PATH or LINK]

---

## Phase 2: Deployment

### 2.1 Which Deployment Strategy?

Choose the appropriate strategy based on risk level:

| Risk Level | Strategy | Description |
|------------|----------|-------------|
| **Low** | Direct full deploy | Ship to 100% of production immediately. Fine for copy changes, non-critical bug fixes. |
| **Medium** | Phased rollout | 10% → 50% → 100%, with 5-minute monitoring windows between phases. |
| **High** | Canary deploy | 1% → 5% → 25% → 100%, with 10-minute monitoring windows and automatic rollback triggers. |

### 2.2 Canary Deploy (High Risk)

Use this for changes that touch auth, payments, the database, infrastructure, or core user flows.

**Step 1: Deploy to canary (1% of traffic).**
- [ ] Deploy the new version to a single instance or a small percentage of traffic.
- [ ] Confirm the canary is receiving traffic (check access logs).
- [ ] Monitor for 10 minutes. Watch: error rate, latency, CPU, memory, crash loops.

**Step 2: Check canary health.**
- [ ] Error rate: [CURRENT] vs baseline [BASELINE] — acceptable? Yes / No
- [ ] Latency (p95): [CURRENT] vs baseline [BASELINE] — acceptable? Yes / No
- [ ] CPU / memory: [CURRENT] vs baseline [BASELINE] — acceptable? Yes / No
- [ ] Any crashes or restarts? Yes / No
- [ ] Manual smoke test on canary: [Describe — "Made a test purchase using canary instance" or "Hit health endpoint and got 200"]
- [ ] **If anything is wrong: STOP. Roll back the canary. Do not proceed.**

**Step 3: Expand to 5%.**
- [ ] Increase canary to 5% of traffic.
- [ ] Monitor for 5 minutes.
- [ ] Same health checks as Step 2.

**Step 4: Expand to 25%.**
- [ ] Increase to 25%.
- [ ] Monitor for 5 minutes.
- [ ] Same health checks.

**Step 5: Full deploy (100%).**
- [ ] Deploy to remaining instances.
- [ ] Monitor continuously (see Phase 3).

**Automatic rollback triggers (if configured):**
- Error rate exceeds [THRESHOLD — e.g., 2x baseline] for [DURATION — e.g., 1 minute]
- Latency p95 exceeds [THRESHOLD]
- Crash loop detected (more than [N] restarts in [M] minutes)
- [CUSTOM_TRIGGER]

Check that automatic rollback is enabled before starting the canary: [Yes / No / N/A]

### 2.3 Phased Rollout (Medium Risk)

**Step 1: Deploy to 10%.**
- [ ] Deploy to ~10% of instances or traffic.
- [ ] Monitor for 5 minutes.
- [ ] Quick health check: error rate, latency, any crashes?

**Step 2: Deploy to 50%.**
- [ ] Deploy to ~50%.
- [ ] Monitor for 5 minutes.
- [ ] Same health checks.

**Step 3: Deploy to 100%.**
- [ ] Full deploy.
- [ ] Monitor continuously.

### 2.4 Direct Deploy (Low Risk)

- [ ] Deploy to all instances.
- [ ] Monitor for at least 5 minutes post-deploy.

---

## Phase 3: Monitoring Checklist (During & Immediately After Deploy)

This is the watching phase. Don't deploy and walk away. Stay at your desk and watch these metrics for at least 15 minutes after the last instance is deployed.

### Monitor These Dashboards

| Metric | Baseline | Post-Deploy | OK? | Dashboard Link |
|--------|----------|-------------|-----|---------------|
| Error rate (5xx) | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| Error rate (4xx) | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| Latency p50 | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| Latency p95 | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| Latency p99 | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| Requests/second | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| CPU (per service) | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| Memory (per service) | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| DB connections | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| DB CPU | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| Queue depth | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| Background job success rate | [VALUE] | [VALUE] | [✓/✗] | [URL] |
| CDN cache hit ratio | [VALUE] | [VALUE] | [✓/✗] | [URL] |

### Check Logs

- [ ] **Application logs:** Any new errors or stack traces that weren't there before? Any deprecation warnings that are suddenly firing at high volume?
- [ ] **Access logs:** Any unexpected 4xx or 5xx patterns?
- [ ] **Database logs:** Any slow queries? Any connection errors?
- [ ] **Third-party integration logs:** Any timeout or error increases?

### Check Alerting

- [ ] **No new alerts fired** during or immediately after deploy. If alerts did fire, investigate before proceeding.
- [ ] **Alert thresholds still appropriate.** Did the deploy change any baselines that make existing alerts too sensitive?

### Manual Smoke Tests

Don't just trust the dashboards — actually use the product.

- [ ] **Core user flow 1:** [Describe — "Log in, create a document, save it, log out"] — Works? Yes / No
- [ ] **Core user flow 2:** [Describe — "Complete a purchase / sign up for a trial"] — Works? Yes / No
- [ ] **Core user flow 3:** [Describe — "Search for content and view results"] — Works? Yes / No
- [ ] **Affected feature test:** The specific feature you changed works correctly in production. Yes / No
- [ ] **Mobile / web / API:** Tested on all relevant platforms? Yes / No

---

## Phase 4: Rollback Decision

### Rollback Decision Tree

```
Something looks wrong post-deploy.
│
├─ Is it definitely caused by this deploy?
│   ├─ Yes → Continue below.
│   └─ No → Investigate. If you can't rule out the deploy within 5 minutes,
│            roll back and investigate with the pressure off.
│
├─ Severity: Are users affected?
│   ├─ Yes, SEV-1 or SEV-2 level → Roll back immediately. Don't debug in production.
│   ├─ Yes, SEV-3 level → You have 10 minutes to find the issue. If not found, roll back.
│   └─ No, but metrics are off → You have 15 minutes. But don't push it.
│
├─ Do you know the fix?
│   ├─ Yes, and it's a one-liner you can deploy in under 5 minutes → Fix forward.
│   └─ No, or the fix is complex → Roll back.
│
└─ Decision: [ROLL BACK / FIX FORWARD / INVESTIGATE]
```

### How to Roll Back

**Step 1: Execute the rollback.**
```bash
# Exact command depends on your infrastructure. Fill this in for each service:

# Option A: Revert to previous deployment
[ROLLBACK_COMMAND_FOR_SERVICE_1]
[ROLLBACK_COMMAND_FOR_SERVICE_2]

# Option B: If using feature flags, disable the flag
[FEATURE_FLAG_DISABLE_COMMAND]

# Option C: Revert the git commit and re-deploy
git revert [COMMIT_SHA]
# Then deploy as normal
```

**Step 2: Verify rollback.**
- [ ] Error rate returned to pre-deploy baseline? [CURRENT] vs [BASELINE]
- [ ] Latency returned to pre-deploy baseline?
- [ ] Manual smoke test passes?
- [ ] No crash loops?

**Step 3: Communicate.**
```
🔙 ROLLBACK: [SERVICE] rolled back to [PREVIOUS_VERSION].

Reason: [One sentence — "Error rate spiked to 12% on the payment API."]
Time deployed before rollback: [DURATION]
Users affected: [BRIEF ESTIMATE]

Investigating root cause now. Will follow up with findings.
```

**Step 4: Post-rollback actions.**
- [ ] Create an incident ticket for the failed deploy.
- [ ] Investigate root cause (with the pressure off).
- [ ] Do NOT re-deploy the same change without understanding what went wrong and adding safeguards.
- [ ] Update this checklist if the rollback procedure didn't work smoothly.

---

## Phase 5: Post-Deploy Validation (15–60 Minutes After Deploy)

The deploy is done and metrics look fine. But some issues take time to surface (slow memory leaks, gradual queue build-up, cron job failures). Check these things after the deploy has been live for a while.

### 15 Minutes Post-Deploy

- [ ] Re-check all Phase 3 metrics. Still normal?
- [ ] Any delayed errors surfacing? (Background jobs that are only now processing, scheduled tasks that just ran.)
- [ ] Are users reporting anything unusual? (Check support tickets, social media, customer Slack/Discord.)

### 30 Minutes Post-Deploy

- [ ] Memory usage stable? (No slow leak trending upward.)
- [ ] Queue depths stable? (Not growing.)
- [ ] Cron jobs that should have run: did they? (Check cron/scheduler dashboard.)

### 60 Minutes Post-Deploy

- [ ] All metrics still normal?
- [ ] Feature-specific KPIs stable? (e.g., "signup conversion rate didn't drop," "payment success rate unchanged.")
- [ ] Post in team Slack: "✅ Deploy of [SERVICE] v[VERSION] is stable after 60 minutes. No issues detected."

---

## Phase 6: Deploy Log

Record the deploy for the team's history. This is useful for correlating incidents later and for the on-call handoff.

| Field | Value |
|-------|-------|
| **Date/Time** | [DATE] [TIME] [TIMEZONE] |
| **Deployer** | [NAME] |
| **Service** | [SERVICE_NAME] |
| **Version / SHA** | [VERSION_OR_COMMIT_SHA] |
| **Change description** | [ONE SENTENCE] |
| **Risk level** | Low / Med / High |
| **Strategy used** | Direct / Phased / Canary |
| **Pre-deploy checks passed** | [COUNT] of [TOTAL] |
| **Any issues during deploy?** | [None / Describe] |
| **Post-deploy validation** | Passed / Issues found (describe) |
| **Rollback?** | No / Yes (reason: [REASON]) |
| **Deploy log link** | [CI/CD PIPELINE URL] |

---

## Emergency Deploy Procedure

When there's an active incident and you need to ship a fix immediately, you can't run the full checklist. But you still need a process.

**What you can skip during an emergency:**
- ✗ Full test suite (run critical tests only)
- ✗ Staging deploy and manual smoke test (test in a canary instead)
- ✗ Team notification beforehand (notify during or immediately after)
- ✗ Full phased rollout (can use direct deploy if the risk of waiting is higher than the risk of deploying)

**What you CANNOT skip, ever:**
- ✓ PR review by at least one other person (even if it's a 30-second "looks good")
- ✓ CI pipeline green (at minimum: build succeeds, critical tests pass)
- ✓ Rollback plan confirmed
- ✓ Monitoring for at least 5 minutes post-deploy
- ✓ Deploy log filled out
- ✓ Team notified (even if after the fact)

**Emergency deploy process:**
1. [ ] Confirm this is an active incident and the fix is urgent.
2. [ ] Get a second set of eyes on the change (PR or pair review).
3. [ ] Deploy directly, but be at your keyboard watching dashboards.
4. [ ] If anything looks wrong in the first 2 minutes, roll back immediately.
5. [ ] Post in the incident Slack channel: "Deploying hotfix for [ISSUE] — [COMMIT_SHA]."
6. [ ] After the incident is resolved, go back and fill out the skipped pre-deploy checks retroactively. Document what was skipped and why in the postmortem.

---

## Multi-Phase Database Changes

If a schema change isn't backward-compatible, you need to deploy it in phases. Don't try to do it all at once.

**Phase 1: Add (backward-compatible).**
- Add the new column/table/index. Don't remove anything. Don't rename anything.
- Deploy. Verify the old code still works with the new schema.

**Phase 2: Migrate and dual-write.**
- Deploy code that writes to both the old and new schema.
- Run a backfill to migrate existing data to the new schema.
- Verify data integrity between old and new.

**Phase 3: Switch reads.**
- Deploy code that reads from the new schema.
- Monitor. If anything breaks, roll back to Phase 2 code (which still writes to both).

**Phase 4: Clean up.**
- Remove writes to the old schema.
- Remove the old column/table.
- Deploy. Celebrate.

**Before starting a multi-phase DB change:**
- [ ] All phases are documented and reviewed.
- [ ] Each phase has a tested rollback path.
- [ ] You've allocated enough time to complete all phases (don't start Phase 1 on Friday afternoon).
- [ ] The team knows a multi-phase migration is in progress and not to deploy conflicting changes.

---

## Deployment Freeze Policy

During freezes, only emergency fixes and pre-approved changes are allowed to deploy.

**Freeze windows:**
- [WINDOW_1_DATES] — Reason: [REASON — e.g., "Holiday code freeze: minimal staff available"]
- [WINDOW_2_DATES] — Reason: [REASON — e.g., "Black Friday / Cyber Monday: maximum traffic, no risk allowed"]
- [WINDOW_3_DATES] — Reason: [REASON]

**Freeze rules:**
1. No routine deploys. No "nice to have" changes. No refactoring.
2. Emergency fixes only, with [APPROVER_NAME] approval.
3. Pre-approved maintenance (e.g., certificate renewals, security patches) can proceed with a second reviewer.
4. Any deploy during freeze must be logged with the freeze exception reason.

**Freeze exception log:**

| Date | Deploy | Reason for exception | Approved by |
|------|--------|---------------------|-------------|
| [DATE] | [DESCRIPTION] | [Why it couldn't wait] | [NAME] |

---

## Appendix: Quick Reference Card

```
PRE-DEPLOY: PR approved ✓ CI green ✓ Staging tested ✓ Rollback plan ✓ Team notified ✓ Dashboard baseline ✓

DEPLOY: Canary(1%→5%→25%→100%) for high-risk, Phased(10%→50%→100%) for medium, Direct for low.

MONITOR (15 min minimum): Errors, latency, CPU, memory, DB connections, queue depth, logs, smoke tests.

ROLLBACK: If SEV-1/SEV-2 → immediate rollback. If SEV-3 → 10 min to find fix, otherwise rollback.
           "I don't know what's wrong" + "it's affecting users" = rollback.

POST-DEPLOY: Recheck at 15, 30, 60 minutes. Log the deploy.

EMERGENCY: PR + CI green + monitor — but you can skip staging and phased rollout.
           Document what you skipped.

FREEZE: Emergency fixes only. [APPROVER] approval required. No exceptions without it.
```
