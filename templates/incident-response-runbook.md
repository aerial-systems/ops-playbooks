# Incident Response Runbook

**Version:** 1.0
**Last updated:** [DATE]
**Owner:** [TEAM_NAME / ON-CALL ROTATION]
**Distribution:** Internal — all engineers on-call

---

## What This Is

This is the single source of truth for how we handle production incidents. It covers severity classification, who to escalate to and when, exactly what the Incident Commander does minute-by-minute, communication templates you can copy-paste, and step-by-step procedures for the incident types we see most often.

If you're on-call and something is on fire, start here. Don't guess. Don't improvise. Follow the checklist.

**One rule above everything else:** if you think we should wake someone up, wake them up. A false alarm at 3 AM is a mildly annoyed colleague. An outage you didn't escalate that burns for six hours is a customer exodus and a very bad day for everyone. Err on the side of escalation.

---

## Severity Classification

Use these definitions to declare a SEV level. If you're not sure between two levels, pick the higher one. You can downgrade later; you can't get back the hour you spent pretending it wasn't SEV-1.

### SEV-1: Critical — Whole Service Down

**Definition:** A core user-facing feature is completely unavailable to all or most users, and there's no workaround. Revenue is actively being lost. Or: a data loss or security breach event is in progress.

**Triggers (any one of these):**
- The primary application returns 5xx errors for > 80% of requests
- Users cannot log in, period
- Payment processing is completely broken
- A PII or credential leak is confirmed
- A database is down or unrecoverable without intervention
- The main site doesn't load at all (not a CDN glitch — actual backend down)

**Response:** Incident Commander declared immediately. All hands. Escalate to engineering manager, CTO, and on-call leadership within 5 minutes. Status page updated within 10 minutes. **No one works on anything else until this is resolved or downgraded.**

**Communication cadence:** Status page update every 30 minutes. Internal Slack update every 15 minutes. Customer-facing email if downtime exceeds 1 hour.

### SEV-2: Major — Key Feature Broken

**Definition:** A significant feature is unavailable or severely degraded for a substantial portion of users. There might be a workaround, but it's painful. Not a full outage, but customers are definitely noticing.

**Triggers:**
- Search is returning empty or wrong results for > 30% of queries
- A primary API endpoint has > 50% error rate
- Notifications/emails are not being delivered
- A critical scheduled job has failed and data is going stale
- The admin dashboard is completely unusable (and someone needs it to operate)

**Response:** Incident Commander declared. Escalate to engineering manager within 15 minutes. Page the relevant service owner. Status page updated within 20 minutes.

**Communication cadence:** Status page every 60 minutes. Internal Slack every 30 minutes.

### SEV-3: Minor — Partial Degradation

**Definition:** A feature is degraded but mostly functional, or the impact is limited to a small subset of users. Customers might notice, but it's not blocking core workflows.

**Triggers:**
- Latency is 2x normal baseline but not causing timeouts
- A non-critical feature (e.g., CSV export, avatar upload) is broken
- Intermittent errors affecting < 10% of requests
- A background job is delayed by more than 1 hour
- A non-production environment is broken and blocking a deploy

**Response:** On-call engineer acknowledges within 30 minutes, investigates during business hours if stable. No Incident Commander required unless it escalates. Status page update optional (use judgment — if customers are asking, post an update).

**Communication cadence:** Internal Slack thread. Status page at discretion.

### SEV-4: Low — Cosmetic / Non-Urgent

**Definition:** Something is wrong but has zero user-facing impact, or the impact is purely cosmetic and no one is complaining.

**Triggers:**
- A dashboard chart is rendering incorrectly
- A non-critical cron job failed once (will retry)
- A staging environment is acting weird
- A deprecation warning that needs attention "soon"

**Response:** File a ticket. No paging, no escalation. Fix during normal work hours.

---

## Escalation Matrix

Here's who gets pulled in and when. Fill in the actual names and contact methods for your team.

| Role | SEV-1 | SEV-2 | SEV-3 | Contact |
|------|-------|-------|-------|---------|
| **Incident Commander** | Immediate | Immediate | Optional | [NAME] — [PHONE/SLACK] |
| **On-Call Engineer (Primary)** | Immediate | Immediate | Within 30 min | [NAME] — [PHONE/SLACK] |
| **On-Call Engineer (Secondary)** | Within 5 min | Within 15 min | — | [NAME] — [PHONE/SLACK] |
| **Engineering Manager** | Within 5 min | Within 15 min | — | [NAME] — [PHONE/SLACK] |
| **CTO / VP Engineering** | Within 10 min | At IC discretion | — | [NAME] — [PHONE/SLACK] |
| **SRE / Infrastructure Lead** | Immediate | Within 10 min | — | [NAME] — [PHONE/SLACK] |
| **Security Lead** | If security-related: immediate | If security-related: within 10 min | — | [NAME] — [PHONE/SLACK] |
| **Product Manager** | Within 30 min | Within 60 min | — | [NAME] — [PHONE/SLACK] |
| **Customer Success Lead** | Within 15 min | Within 30 min | — | [NAME] — [PHONE/SLACK] |
| **CEO** | CTO decides | — | — | [NAME] — [PHONE/SLACK] |

### Escalation Path (by team/service)

If the primary on-call doesn't acknowledge within the timeframe, the alert escalates automatically:

```
Primary On-Call (5 min for SEV-1, 15 min for SEV-2)
  → Secondary On-Call (another 5 min)
    → Engineering Manager (another 5 min)
      → CTO
```

### What "Escalate" Actually Means

It means: call or Slack the person. Don't just send a message and hope they see it. If it's SEV-1 and it's outside business hours, you call. PagerDuty/Opsgenie should handle this automatically, but know the backup path: actual phone numbers written down somewhere you can access even if the internet is down. (Yes, really. Put them in your phone. Put them on a piece of paper in your wallet. The day you need them is the day Slack, PagerDuty, and your password manager are all unreachable.)

**Backup phone list — print this:**

| Name | Number | SEV-1? | Notes |
|------|--------|--------|-------|
| [NAME] | [PHONE] | Yes | Primary on-call |
| [NAME] | [PHONE] | Yes | Secondary |
| [NAME] | [PHONE] | Yes | EM |
| [NAME] | [PHONE] | Yes | CTO |

---

## Incident Commander Checklist

The Incident Commander (IC) is the person running the incident. By default, this is the primary on-call engineer. Anyone can hand off the IC role, but at any moment exactly one person is IC. The IC's job is not to fix the problem — it's to run the process so the people fixing the problem can focus.

### Minute 0–5: Declare and Assemble

- [ ] **Acknowledge the alert.** If PagerDuty is paging, ack it so it doesn't escalate to the next person.
- [ ] **Open the incident Slack channel.** Name it `#incident-YYYY-MM-DD-brief-description` (e.g., `#incident-2026-06-25-payment-api-down`).
- [ ] **Post the initial status** using the Slack template below.
- [ ] **Declare SEV level.** If unsure, go higher. You can downgrade later.
- [ ] **Assign yourself (or someone) as IC.** Say it explicitly: "I'm IC on this. [Name] is on investigation."
- [ ] **Start a shared document** (Google Doc, Notion) for the live timeline. Link it in the Slack channel topic.
- [ ] **Start a timer.** You need to know exactly how long this has been going.

### Minute 5–15: Triage and Communicate

- [ ] **Check dashboards.** What's actually broken? Don't trust the alert — verify. Check Datadog/Grafana/your observability tool. Look at error rates, latency, traffic, saturation.
- [ ] **Identify the blast radius.** What's affected? Which services? Which customer segments? Which regions?
- [ ] **If this is a deployment-related issue, find the deploy.** What was the last change? Who shipped it? When?
- [ ] **Pull in responders.** Anyone who owns a service in the blast radius gets pulled into the Slack channel. Don't wait for them to volunteer.
- [ ] **Post first status page update** (if SEV-1 or SEV-2). Use the template.
- [ ] **Notify Customer Success** so they know what to tell customers who reach out.

### Minute 15–30: Run the Investigation

- [ ] **Delegate investigation threads.** "Alice, you're on the database. Bob, you're checking the deploy. Carol, can you look at the CDN logs?"
- [ ] **Keep the timeline.** Every significant action goes in the shared doc with a timestamp. This isn't busywork — it's how you do the postmortem and how you communicate to stakeholders who join late.
- [ ] **Call out dead ends publicly.** If someone says "I thought it was X but it's not," put that in Slack and the timeline. It saves the next person from re-investigating X.
- [ ] **Post internal Slack update** at the 15-minute mark (even if the update is "still investigating").

### Ongoing: Until Resolved

- [ ] **Rotate people out.** If someone has been staring at logs for 90 minutes, make them take a break. Tunnel vision is real.
- [ ] **Escalate if stalling.** If you haven't identified the root cause in 30 minutes on a SEV-1, escalate. Pull in more senior engineers. Call the CTO. Pay the AWS support bill. Do whatever it takes.
- [ ] **Consider rollback.** If a recent deploy is in the blast radius, roll it back. Don't spend 45 minutes debugging a bad deploy — just revert and debug later.
- [ ] **Post status updates on cadence.** Set a timer. Don't miss an update window.
- [ ] **Watch for scope creep.** If someone starts fixing a "related" issue that isn't part of this incident, ask them to stop. Stay focused.
- [ ] **If customers are reporting it on social media**, have someone (PM, CS, or marketing) acknowledge it publicly. "We're aware of an issue affecting [feature] and are investigating" is fine. Silence looks like you don't know or don't care.

### Resolution

- [ ] **Declare the incident resolved.** Say it in Slack. Post to the status page. "The issue has been resolved. [Brief description of what happened and what was fixed.] We are monitoring."
- [ ] **Monitor for 30 minutes.** Do not close the incident channel until you've watched dashboards for at least 30 minutes and confirmed everything is stable.
- [ ] **Schedule the postmortem.** Within 5 business days for SEV-1, within 10 for SEV-2. Put it on the calendar before you close the incident.
- [ ] **File follow-up action items as tickets.** Every "we should fix that" from the incident becomes a real ticket with an owner and a deadline. Do not let them die in the postmortem doc.

---

## Communication Templates

### Slack: Initial Incident Declaration

Copy, paste, fill in the brackets.

```
🚨 INCIDENT DECLARED: [SEV-1/2] — [Brief description]

**What we know:**
- [What's broken, what users are seeing]
- Started: [TIME] [TIMEZONE]
- Detected by: [Alert name / manual report]

**Blast radius:**
- Services affected: [list]
- Regions affected: [list]
- Estimated user impact: [X% of users / "all users"]

**Current status:** Investigating. Root cause unknown.

**IC:** [@name]
**Responders:** [@name, @name]

**Live timeline doc:** [link]

I'll post updates in this thread every [15/30] minutes.
```

### Slack: Status Update (every 15 for SEV-1, every 30 for SEV-2)

```
📊 UPDATE — [TIME] ([X] min since declaration)

**What's changed since last update:**
- [New finding, ruled-out theory, or action taken]

**Current theory:** [What we think is happening]
**Mitigation in progress:** [What we're doing about it]
**ETA to resolution:** [If known. "Unknown" is an acceptable answer.]

**Still investigating.** Next update at [TIME].
```

### Slack: Resolution

```
✅ RESOLVED — [TIME] (Total duration: [X] min)

**What happened:** [One-sentence summary]
**What we did:** [Brief description of fix]
**Impact:** [Users affected, duration, any data loss?]

**Monitoring:** We'll watch dashboards for 30 minutes before closing.
**Postmortem:** Scheduled for [DATE/TIME]. Link to doc: [link]

Great work everyone.
```

### Status Page: Initial Incident

**Title:** [Brief, human-readable — "Payment processing is currently unavailable"]

**Body:**
```
We are currently investigating an issue affecting [feature/service]. [What users are experiencing — "Users may see errors when attempting to complete a purchase."]

Our engineering team is actively working to identify the root cause. We will post an update within 30 minutes.

Posted: [TIME] [TIMEZONE]
```

### Status Page: Update

```
**Update [TIME] [TIMEZONE]:** We have identified the issue as [brief explanation] and are working on a fix. [What users should expect — "The service remains degraded. We expect resolution within the next 30 minutes."]
```

### Status Page: Resolution

```
**Update [TIME] [TIMEZONE]:** This incident has been resolved. [One sentence about what happened and what was done.] The service is now operating normally. We will publish a postmortem with more detail in the coming days.

We apologize for the disruption. If you continue to experience issues, please contact [support@company.com].
```

### Customer-Facing Email (if downtime > 1 hour)

**Subject:** [Issue] affecting [Your Product Name] — Update

```
Hi [Name / everyone],

We wanted to let you know that [service/feature] experienced an outage from approximately [start time] to [end time] [timezone] on [date].

**What happened:** [Plain-English explanation. Be honest. Don't blame "an upstream provider" if it was a config error on your end.]

**Impact:** [What customers experienced. Be specific — "23 minutes where payments could not be processed" is better than "intermittent issues."]

**What we're doing:** [One sentence about the fix, and one about prevention — "We've corrected the configuration and added a safeguard to prevent similar issues in the future."]

We take reliability seriously, and we're sorry for the disruption. A detailed postmortem is available here: [link].

If you have any questions, please reply to this email or reach out to [support@company.com].

— The [Company Name] Engineering Team
```

---

## Blameless Postmortem Template

This is the document you fill out after the incident is resolved. The goal is to understand what happened and prevent it from happening again — not to assign fault. "Blameless" doesn't mean "no accountability." It means we focus on the system that allowed the mistake, not the person who made it.

Copy this into a new doc, fill it out, and review it with the team within the postmortem window (5 business days for SEV-1, 10 for SEV-2).

---

### Postmortem: [Incident Title]

| Field | Value |
|-------|-------|
| **Date of incident** | [DATE] |
| **SEV level** | [1/2/3] |
| **Duration** | [START] to [END] ([DURATION]) |
| **Incident Commander** | [NAME] |
| **Postmortem owner** | [NAME] |
| **Postmortem date** | [DATE] |
| **Status** | Draft / In Review / Final |

---

### Executive Summary

[2–3 sentences. What broke, for how long, and what the customer impact was. Someone reading only this section should understand the incident.]

Example: *"The payment API returned 500 errors for 34 minutes on June 25th, affecting approximately 15,000 checkout attempts. The root cause was a misconfigured database connection pool limit introduced in a deployment at 14:22 UTC. The issue was resolved by rolling back the deployment. No payments were double-charged and no data was lost."*

---

### Timeline (All times in UTC)

| Time | Event |
|------|-------|
| 14:22 | Deploy v2.41.0 to production (reduced connection pool size from 50 to 10) |
| 14:24 | First signs of elevated latency in payment API (noticed in postmortem, not alerted) |
| 14:28 | Connection pool exhausted; payment API begins returning 500 errors |
| 14:31 | PagerDuty alert fires: "Payment API error rate > 5%" |
| 14:32 | On-call acknowledges, opens incident Slack channel |
| 14:35 | Incident Commander declared; SEV-1 |
| 14:38 | Initial investigation begins — checking recent deploys |
| 14:41 | Database team identifies connection pool saturation |
| 14:44 | Rollback decision made |
| 14:47 | Rollback complete; error rate drops to zero |
| 14:51 | Services confirmed stable; monitoring |
| 15:21 | Incident resolved after 30-minute monitoring window |

---

### What Went Well

- [ ] [Something that worked — e.g., "Alert fired within 3 minutes of error rate crossing threshold"]
- [ ] [Something that worked — e.g., "Rollback was fast and clean because we practice it"]
- [ ] [Something that worked — e.g., "Slack channel was organized and timeline was well-maintained"]

---

### What Went Wrong

- [ ] [What broke — be specific about the system, not the person. "The connection pool default of 10 was not validated against production traffic patterns" not "Bob made a bad config change."]
- [ ] [Process gap — "No canary deploy was used for this change. It went to 100% of production immediately."]
- [ ] [Tooling gap — "Our alerting didn't catch the latency increase before it became a hard failure."]

---

### Root Cause Analysis

**What was the root cause?** [Be specific. Use "Five Whys" if helpful.]

*Why did the payment API return 500s?*
Because the database connection pool was exhausted.

*Why was the pool exhausted?*
Because the pool size was reduced from 50 to 10.

*Why was it reduced?*
Because a developer changed a configuration value to match a recommendation from a blog post without testing it against our production traffic.

*Why wasn't it tested?*
Because our staging environment doesn't use production-scale traffic patterns, and we don't have load-testing integrated into our deployment pipeline.

*Why don't we have load testing in the deployment pipeline?*
Because it hasn't been prioritized. (This is the real root cause — the gap that, if fixed, prevents this entire class of incident.)

---

### Action Items

Every action item must have an owner and a due date. No "we should look into this eventually."

| # | Action | Owner | Due Date | Priority | Ticket Link |
|---|--------|-------|----------|----------|-------------|
| 1 | Add connection pool validation to CI/CD pipeline (fail deploy if pool size < production minimum) | [NAME] | [DATE] | P0 | [LINK] |
| 2 | Implement canary deploys for the payment service | [NAME] | [DATE] | P1 | [LINK] |
| 3 | Add latency-based alerting so we catch pool exhaustion before hard failure | [NAME] | [DATE] | P1 | [LINK] |
| 4 | Run load tests against staging before any config changes in production | [NAME] | [DATE] | P2 | [LINK] |
| 5 | Review all current production config values and document safe ranges | [NAME] | [DATE] | P2 | [LINK] |

---

### Lessons Learned

[2–3 bullet points that the broader engineering org should take away. These go in a shared "incident lessons" doc or get presented at an all-hands.]

- Changing resource limits (connection pools, memory, threads) in production without load testing is dangerous. We need guardrails.
- Our alerting caught the hard failure but missed the leading indicator (rising latency). Latency-based alerts would have given us 4 extra minutes.
- Fast rollback matters. The rollback process was smooth because we practice it. Keep doing that.

---

## Runbooks for Common Incidents

These are step-by-step procedures for the incidents we see most often. If you're on-call and one of these fires, follow the runbook. Don't improvise. If the runbook doesn't work, update it after the incident.

### Runbook: Database Outage

**Symptoms:** Application returns 5xx errors mentioning "connection refused," "timeout," or "too many connections." Database dashboards show high CPU, no available connections, or the instance is unreachable.

**Step 1: Verify it's the database.**
```bash
# Try connecting directly
psql -h [DB_HOST] -U [USER] -d [DATABASE] -c "SELECT 1;"
# Or MySQL:
mysql -h [DB_HOST] -u [USER] -p -e "SELECT 1;"
```
If this fails with "connection refused" or times out, it's the DB. If it succeeds but is slow, check step 3.

**Step 2: Check the database status.**
- Log into the cloud provider console (AWS RDS / GCP Cloud SQL / etc.).
- Check: Is the instance running? Any automated failover in progress? Any maintenance events?
- Check: CPU, memory, IOPS, and connection count dashboards.
- If using RDS: check `recent_events` in the console for failovers.

**Step 3: If connection pool is exhausted (most common):**
```sql
-- Check current connections (PostgreSQL)
SELECT count(*) FROM pg_stat_activity;

-- Find idle connections
SELECT pid, state, query_start, query
FROM pg_stat_activity
WHERE state = 'idle' OR state = 'idle in transaction'
ORDER BY query_start;

-- Kill idle connections older than 10 minutes (CAREFUL — verify first)
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE state = 'idle in transaction'
  AND query_start < now() - interval '10 minutes';
```

**Step 4: If the instance is completely unreachable:**
- Check if a failover to a read replica is possible. Most managed databases support this.
- If no replica is available, contact the cloud provider support (AWS/GCP support ticket + phone if you have a support plan).
- In parallel: start restoring from the latest backup. This takes time; start early.

**Step 5: Once the database recovers:**
- Restart application servers to clear any stale connection pools.
- Verify all services reconnect successfully.
- Monitor for 15 minutes.

**Need to know:**
- [DB_HOST]: [VALUE]
- [DB_TYPE]: [PostgreSQL / MySQL / etc.]
- [CLOUD_PROVIDER_CONSOLE_URL]: [URL]
- [BACKUP_RESTORE_DOCS]: [URL]
- [SUPPORT_PLAN_ID]: [VALUE]

---

### Runbook: Traffic Spike / Overload

**Symptoms:** Elevated latency across all endpoints, increasing error rate, application servers at high CPU, queue depths growing. Looks like a DDoS but might be organic (launch, press mention, viral post).

**Step 1: Check if it's an attack or organic.**
- Look at traffic sources in your CDN/load balancer logs. Is it coming from a few IPs or a normal distribution?
- Check user-agent strings. Bots? Scripts?
- Check if it's hitting a single expensive endpoint or spread across the app.
- Check if there's a press mention, Product Hunt launch, or social media spike that explains it.

**Step 2: If it's an attack / abusive traffic:**
- Rate-limit the offending IPs or patterns at the CDN/load balancer level.
- If using Cloudflare: enable "I'm Under Attack" mode.
- If specific endpoints are targeted: temporarily block them or add aggressive caching.
- Contact your CDN/infra provider's security team if it's large-scale.

**Step 3: If it's organic traffic (good problem to have):**
- Scale up application servers immediately. Don't wait for autoscaling — it might be too slow.
  ```bash
  # Example: scale an ECS service
  aws ecs update-service --cluster [CLUSTER] --service [SERVICE] --desired-count [NEW_COUNT]
  ```
- If you have a read replica for the database, route read queries to it.
- Enable aggressive caching at the CDN level. Cache everything you can, even for short TTLs (30 seconds).
- Disable non-critical features temporarily: turn off analytics tracking, complex search, recommendation engines.

**Step 4: If none of that is enough (queues are still growing, errors are increasing):**
- Implement a queue/dead letter queue for async work. Accept the work, process it later. Better to delay a notification by 10 minutes than to drop it entirely.
- As a last resort: serve a static "We're experiencing high demand" page for non-authenticated users while keeping the core app running for logged-in users.

**Step 5: Once traffic subsides:**
- Scale back down gradually (not all at once — you might need to scale up again).
- Re-enable any disabled features.
- Document the peak traffic numbers for capacity planning.

**Need to know:**
- [CDN_DASHBOARD_URL]: [URL]
- [LOAD_BALANCER_DASHBOARD_URL]: [URL]
- [AUTOSCALING_CONFIG]: [WHERE TO FIND IT]
- [RATE_LIMIT_CONFIG]: [WHERE TO FIND IT]

---

### Runbook: TLS Certificate Expiry

**Symptoms:** Users report "connection not secure" errors or can't reach the site. Your monitoring should also alert you *before* this happens, but here we are.

**Step 1: Verify it's a cert issue.**
```bash
# Check a certificate's expiry date
echo | openssl s_client -servername [DOMAIN] -connect [DOMAIN]:443 2>/dev/null | openssl x509 -noout -dates

# Or use curl
curl -vI https://[DOMAIN] 2>&1 | grep -i "expire\|certificate"
```

**Step 2: If you use a managed certificate service (AWS ACM, Let's Encrypt with auto-renewal):**
- Check the certificate status in the console.
- For ACM: certificates auto-renew but need to be associated with the resource (CloudFront, ALB). Sometimes the association breaks.
- For Let's Encrypt: check if the certbot cron job is running.
  ```bash
  sudo systemctl status certbot.timer
  sudo certbot renew --dry-run
  ```

**Step 3: If you need to manually renew/replace:**
- Generate a new certificate from your provider.
- Apply it to the load balancer, CDN, or web server.
- Verify immediately:
  ```bash
  curl -vI https://[DOMAIN] 2>&1 | grep -i "expire\|issuer\|subject"
  ```

**Step 4: After fixing:**
- Set up monitoring to alert 30 days before expiry.
  ```bash
  # Simple check script you can run as a cron job
  EXPIRY=$(echo | openssl s_client -servername [DOMAIN] -connect [DOMAIN]:443 2>/dev/null | openssl x509 -noout -enddate | cut -d= -f2)
  DAYS_LEFT=$(( ($(date -d "$EXPIRY" +%s) - $(date +%s)) / 86400 ))
  if [ $DAYS_LEFT -lt 30 ]; then
    echo "WARNING: Certificate for [DOMAIN] expires in $DAYS_LEFT days"
  fi
  ```
- Document the renewal process. If it's manual, put it on a shared calendar with reminders.

**Need to know:**
- [ALL_DOMAINS_WITH_CERTS]: [LIST THEM]
- [CERT_PROVIDER]: [AWS ACM / Let's Encrypt / DigiCert / etc.]
- [CERT_CONSOLE_URL]: [URL]
- [CERT_RENEWAL_PROCESS_DOC]: [URL]

---

### Runbook: Bad Deployment

**Symptoms:** Errors start immediately after a deployment. You can correlate the timing with a deploy timestamp. This is the most common cause of incidents, and the fix is almost always the same: roll back.

**Step 1: Confirm it's deploy-related.**
- Check the deploy log. When was the last deploy? What changed?
  - [DEPLOY_LOG_URL]: [URL]
  - [CI/CD_PIPELINE_URL]: [URL]
- If the deploy was within the last 30 minutes and the incident started after it, assume it's deploy-related until proven otherwise.

**Step 2: Decide: fix-forward or rollback?**
- **Roll back if:** the fix is not obvious within 10 minutes, the deploy touches critical infrastructure, or the incident is SEV-1.
- **Fix forward if:** you know exactly what the issue is, the fix is a one-liner, and you can deploy it in under 5 minutes. (Be honest with yourself here. Almost always roll back.)

**Step 3: Roll back.**
```bash
# The exact command depends on your setup. Examples:

# If using git tags for deploys:
git checkout [PREVIOUS_TAG]
# Deploy that tag

# If using a CI/CD pipeline with a rollback button:
# Click the "Rollback" button in [CI/CD_TOOL]

# If using Kubernetes:
kubectl rollout undo deployment/[DEPLOYMENT_NAME] -n [NAMESPACE]

# If using ECS:
aws ecs update-service --cluster [CLUSTER] --service [SERVICE] \
  --task-definition [PREVIOUS_TASK_DEF_ARN]
```

**Step 4: Verify the rollback.**
- Check dashboards. Did error rate drop? Latency normalize?
- Check the application health endpoint.
- Confirm with a manual request to the affected endpoint.

**Step 5: Communicate.**
- Post in Slack: "Rollback to [VERSION/TAG] is complete. Error rates are returning to normal. Monitoring."
- Update the status page.

**Step 6: Post-incident.**
- Do NOT re-deploy the bad version without understanding what went wrong.
- The person who shipped the bad deploy owns the investigation and the postmortem action items. This isn't punishment — it's context. They know what changed.

**Need to know:**
- [ROLLBACK_COMMAND]: [EXACT COMMAND FOR YOUR SETUP]
- [DEPLOY_DASHBOARD_URL]: [URL]
- [PREVIOUS_STABLE_TAG]: [CHECK BEFORE YOU NEED IT]

---

### Runbook: Third-Party Dependency Down

**Symptoms:** Errors from a specific integration (payment processor, email service, SMS provider, authentication provider). Your app is up, but something it depends on is not.

**Step 1: Confirm it's the third party.**
- Check the third party's status page: [LIST OF STATUS PAGES BELOW]
- Check your own error logs. Are the errors all from calls to `[api.thirdparty.com]`?
- Try a manual request:
  ```bash
  curl -v https://api.[THIRD_PARTY].com/health
  ```
- If you can't reach them at all, it's them.

**Step 2: Check if you have a fallback.**
- Is there a retry mechanism? How many retries? What's the backoff?
- Is there a circuit breaker? Has it tripped?
- Can you switch to a backup provider? (e.g., secondary email service, secondary SMS provider)

**Step 3: If there's no fallback (most cases):**
- **Don't keep hammering the broken service.** If you have retry logic with exponential backoff, make sure it's working. If not, consider pausing the integration temporarily to avoid building up a massive failure backlog.
- For async operations (emails, SMS, webhooks): queue them. They'll process when the service recovers.
- For sync operations (payments, auth): you need to decide whether to fail gracefully or block the user flow. In most cases, show a clear error message: "We're unable to process payments right now. Please try again in a few minutes. Your cart has been saved."

**Step 4: Communicate.**
- Status page: "We're currently experiencing issues with [feature] due to an upstream provider outage. [Provider] is aware and working on a resolution. We'll update when we have more information."
- Link to the third party's status page so customers can verify independently.

**Step 5: When the third party recovers:**
- Process any queued items.
- Verify integration is working end-to-end.
- Update status page.

**Status pages for our critical dependencies:**

| Service | Status Page URL | What breaks if it's down |
|---------|----------------|--------------------------|
| [SERVICE] | [URL] | [IMPACT] |
| [SERVICE] | [URL] | [IMPACT] |
| [SERVICE] | [URL] | [IMPACT] |

---

## Incident Closing Checklist

Before you close the incident and go back to normal work:

- [ ] **Status page updated** with resolution message.
- [ ] **Slack channel topic updated** to "[RESOLVED] — [brief description]"
- [ ] **All responders thanked** in Slack. Seriously, do this. It matters.
- [ ] **Timeline doc finalized.** Move it from "live notes" to a clean document.
- [ ] **Postmortem scheduled.** On the calendar, with invites sent. Within 5 business days for SEV-1, 10 for SEV-2.
- [ ] **Follow-up tickets filed.** With owners and due dates.
- [ ] **On-call handoff updated** (see the On-Call Handoff Template — the next on-call person needs to know about this).
- [ ] **Alert tuning:** If any alerts were noisy or misleading during this incident, file a ticket to tune them.
- [ ] **Runbook updated:** If anything in this runbook was wrong, missing, or out of date, fix it now while it's fresh.

---

## Appendix: Quick Reference Card

Print this. Stick it on the wall. Save it on your phone. When it's 3 AM and you're half awake, this is all you need to read.

```
SEV-1: Whole service down → IC immediately → Slack + status page in 5 min → EM + CTO paged
SEV-2: Major feature broken → IC immediately → Slack + status page in 10 min → EM paged
SEV-3: Degraded → Acknowledge in 30 min → Fix during business hours
SEV-4: Cosmetic → Ticket → Fix whenever

IC job: Run process, not fix. One person IC at a time.
  1. Acknowledge alert
  2. Open incident Slack channel
  3. Declare SEV
  4. Start timeline doc
  5. Pull in responders
  6. Post status updates on cadence
  7. Keep everyone focused

Slack channel naming: #incident-YYYY-MM-DD-short-description

Rollback is almost always the right answer for deploy-related incidents.
Escalate early. A false alarm is better than a silent outage.
Postmortems are mandatory for SEV-1 and SEV-2. Blameless.
```
