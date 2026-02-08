# PLAYBOOK: Phishing Email Response (SOC L1)

## What this is for
Use this when a suspicious email is reported or flagged. Aim is to (1) confirm what it is, (2) contain fast, (3) work out impact, (4) leave the ticket audit-ready.

## Triggers
- User reports suspicious email
- Email security tool flags phishing indicators
- Multiple users report the same message
- Spike in MFA prompts / risky sign-ins linked to the same timeframe

---

## 0) Quick gate: severity + escalation

### Severity (quick guide)
- **Low:** obvious spam, no interaction, no business risk
- **Medium:** link/attachment present but no interaction
- **High:** click/open occurred, convincing impersonation, or multiple recipients
- **Critical:** credentials entered, BEC/payment diversion, confirmed compromise, malware confirmed

### Escalate immediately if any apply
- [ ] Credentials entered OR OAuth consent granted
- [ ] Suspicious mailbox rules/forwarding found
- [ ] MFA fatigue / abnormal MFA prompts reported
- [ ] Malware detonation positive OR EDR alert present
- [ ] VIP targeted OR multiple users affected
- [ ] Strong signs of account compromise (impossible travel, high-risk sign-in)

**If escalated:** record who/when, then continue containment steps in parallel where allowed.

---

## 1) Immediate actions (target: 0–15 minutes)
- [ ] Confirm user actions: opened / clicked / attachment / credentials / replied
- [ ] Capture basics: sender, subject, receive time, user mailbox, Message-ID
- [ ] Preserve evidence: save `.eml` or copy full headers (whatever your process allows)
- [ ] Check if this is already a known campaign (similar reports, same subject/sender/URL)
- [ ] Set initial severity and note rationale

---

## 2) Containment (target: 15–60 minutes)

### Email containment (Messaging / Email Security)
- [ ] Quarantine the message (if supported)
- [ ] Search and purge/delete from affected mailboxes (campaign scope permitting)
- [ ] Block sender address/domain (mail security)
- [ ] Block malicious URLs/domains (mail security + web proxy/DNS filtering if available)

### Account containment (IAM / SOC)
Only if interaction suggests risk (click/creds) or sign-ins look bad:
- [ ] Force password reset
- [ ] Revoke sessions/tokens
- [ ] Remove suspicious mailbox rules/forwarding
- [ ] Review OAuth consents / suspicious app access (if applicable)

### Endpoint containment (Endpoint / EDR)
Only if attachment opened/executed or malware suspected:
- [ ] Run EDR scan and review alerts
- [ ] Isolate host (per policy/approval)
- [ ] Collect triage package/logs (per policy)

---

## 3) Investigation

### 3.1 Initial investigation (target: 15–60 minutes)
Goal: decide “what is it?” and “who is at risk?” quickly.

**Email / headers**
- [ ] Review Received chain and Reply-To vs From mismatch
- [ ] Note spoofing indicators (display name, lookalike domain)
- [ ] Record SPF / DKIM / DMARC results

**URLs**
- [ ] Extract all URLs/domains
- [ ] Check reputation (malicious/suspicious/unknown)
- [ ] Note redirect chain if visible
- [ ] Identify credential-harvest patterns (fake login portals, brand impersonation)

**Attachments (static-first)**
- [ ] Confirm file type matches extension
- [ ] Hash attachment (SHA256) and check reputation
- [ ] Look for macros/scripts/executables indicators
- [ ] If approved: sandbox/detonation verdict recorded

**Scope**
- [ ] Single vs multiple recipients
- [ ] Any VIPs / sensitive teams
- [ ] Any evidence of interaction (user statement + logs if available)

### 3.2 Deeper investigation (target: 1–4 hours+ as needed)
Use this if escalation triggers are hit, multiple users are impacted, BEC risk exists, or interaction occurred.

**Mailbox / M365**
- [ ] Inbox rules, forwarding, delegates
- [ ] Suspicious sent items/deleted items around the timeframe
- [ ] OAuth consents / suspicious app access

**Identity**
- [ ] Sign-in anomalies (IP, location, device, risk)
- [ ] Password reset history / MFA method changes
- [ ] Failed logon spikes (spray/brute force indicators)

**Endpoint (if needed)**
- [ ] Process tree, persistence, suspicious network connections
- [ ] Downloads created / unusual executables
- [ ] Other hosts showing similar IOCs

---

## 4) Eradication (target: 2–8 hours)
- [ ] Remove malicious rules/forwarding/delegates
- [ ] Remove suspicious OAuth consents / unauthorized app access (where applicable)
- [ ] Confirm blocks are in place (sender/domain/URLs as appropriate)
- [ ] Ensure compromised credentials are reset and sessions revoked
- [ ] If malware confirmed: follow endpoint eradication procedure (L2/IR as required)

---

## 5) Recovery (target: 4–24 hours)
- [ ] Validate user access restored with known-good credentials
- [ ] MFA re-enrolled/re-verified if required
- [ ] Mailbox settings verified (rules/forwarding/delegates clean)
- [ ] Confirm no further suspicious sign-ins post-remediation
- [ ] Monitor affected accounts/hosts for 24–72 hours (per policy)

---

## 6) Post-incident (target: 1–7 days)
- [ ] Incident report completed (what, impact, actions, outcome)
- [ ] Timeline captured (receive → interact → detect → contain → recover)
- [ ] IOCs documented (sender/domain/URLs/hashes; IPs only if meaningful)
- [ ] Detection tuning request raised (what would have caught it earlier?)
- [ ] User comms/awareness notes logged
- [ ] Ticket closed with evidence references

---

## Evidence checklist (minimum)
- [ ] `.eml` saved OR full headers captured
- [ ] Message-ID recorded
- [ ] URLs captured (original + redirected if known)
- [ ] Attachment details + SHA256 (if present)
- [ ] SPF/DKIM/DMARC recorded
- [ ] User interaction summary + timestamps recorded
- [ ] Actions taken recorded (purge/block/reset/revoke/isolate)

---

## Investigation queries / examples (adapt to your environment)

### Microsoft 365 / Exchange Online (examples)

**Unified Audit Log (user activity)**
```powershell
Search-UnifiedAuditLog -StartDate (Get-Date).AddDays(-7) -EndDate (Get-Date) -UserIds user@domain.com
