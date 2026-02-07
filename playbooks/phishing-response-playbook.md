# Phishing Response Playbook (SOC L1)

## Purpose

Provide a repeatable, SOC-friendly response flow for suspected phishing emails, from triage through containment, remediation, and post-incident improvements.

## Triggers

- End-user reports a suspicious email
- Email screening tool (e.g., *Phishtool*) flags indicators of phishing

---

## Quick Decision Gate

### Severity guide

- **Low:** clear spam, no interaction
- **Medium:** link/attachment present, no evidence of interaction
- **High:** link/attachment opened **or** convincing impersonation **or** multiple recipients
- **Critical:** credentials entered, confirmed compromise, BEC/payment diversion attempt, malware confirmed

### Escalate immediately if (tick any that apply)

- [ ] Credentials entered **or** OAuth consent granted
- [ ] Suspicious mailbox rules/forwarding discovered
- [ ] MFA fatigue / abnormal MFA prompts reported
- [ ] Malware detonation positive **or** EDR alert present
- [ ] VIP targeted **or** multiple users affected
- [ ] Confirmed account compromise indicators (e.g., impossible travel, high-risk sign-in)

> **If any box is ticked:** escalate to IR/IAM/Messaging teams per your org process before closing the case.

---

## Evidence to Capture (minimum)

- [ ] Original email saved (`.eml`) and preserved
- [ ] Full headers captured
- [ ] Sender address, display name, Reply-To, Message-ID recorded
- [ ] URLs captured (original + redirected chain if known)
- [ ] Attachment details captured (name, type) and hash recorded (SHA256)
- [ ] SPF / DKIM / DMARC results recorded
- [ ] User interaction summary + timestamps captured (clicked/opened/credentials entered)

---

## 1) Identify & Triage

### 1.1 Collect email artefacts

- Sender (display + address), Reply-To, To/CC
- Message-ID
- Originating IP (if available)
- URLs and domains referenced
- Attachments (names, types, hashes)
- Header authentication results: SPF / DKIM / DMARC

### 1.2 Initial analysis

- Compare sender domain vs claimed brand/org (lookalike domains, typosquatting)
- Review language indicators (urgency, threats, rewards, “verify now”)
- Check for credential-harvesting patterns (login pages, fake Microsoft/Google prompts)
- Identify whether multiple users received the same email

### 1.3 Enrichment (tool-agnostic)

Run reputation/enrichment against indicators:

- Domains/URLs (malicious/suspicious/unknown, newly registered domain check if available)
- Resolved destination IPs (if applicable)
- Attachment hash reputation
- Redirect chain notes (shorteners, multiple hops)

### 1.4 Attachment handling (safe, static-first)

If an attachment exists:

- Perform **static checks** first (file extension mismatch, macros/scripts, executable content)
- Record hash (SHA256)
- If your org allows, submit to approved sandbox/detonation platform and record verdict

### 1.5 Determine verdict

Set verdict:

- **Malicious / Suspicious-Unknown / Spam / Benign**

---

## 2) Contain & Mitigate

### 2.1 Email containment (Messaging / Email Security)

- [ ] Quarantine the message (where supported)
- [ ] Search and purge/delete from affected mailboxes
- [ ] Block sender address/domain (mail security controls)
- [ ] Block malicious URLs/domains (email security + web proxy/DNS filtering where available)

### 2.2 Endpoint containment (Endpoint / EDR)

If link clicked or attachment opened/executed:

- [ ] Initiate endpoint scan for IoCs/payload indicators
- [ ] Review EDR alerts tied to the host/user timeframe
- [ ] Isolate host in EDR (if policy permits / with approval)
- [ ] Collect triage package/logs (if policy permits)

### 2.3 Scope assessment (SOC)

- Identify impacted users (single vs multiple)
- Check whether any credentials were entered
- Confirm whether any mailbox rules/forwarding were created
- Confirm whether similar emails are present in other mailboxes

---

## 3) Remediate & Recover

### 3.1 Identity actions (IAM / SOC)

If credentials entered or compromise suspected:

- [ ] Force password reset
- [ ] Revoke sessions/tokens
- [ ] Reset/re-register MFA (if required by policy)
- [ ] Disable account temporarily (if confirmed compromise)

### 3.2 Mailbox cleanup (Messaging)

- [ ] Remove malicious inbox rules/forwarding
- [ ] Check for suspicious delegate access / app consents (if applicable)

### 3.3 User communications (SOC)

- Notify affected users of outcome and required actions
- Provide guidance:
  - do not interact further
  - report similar emails
  - confirm password reset/MFA steps if performed
- If widespread: send wider awareness note via approved comms channels

### 3.4 Prevent recurrence (Detection/Email Security)

- [ ] Add IOCs (domains/URLs/hashes) to blocklists/watchlists where appropriate
- [ ] Create/update rule(s) to detect similar patterns (subject, sender, URL pattern, attachment traits)

---

## 4) Post-Incident Review

### 4.1 Document findings

Include:

- Timeline of key events (received → interacted → detected → contained)
- Indicators observed (URLs, domains, hashes, sender details)
- Scope (users/mailboxes/hosts affected)
- Actions taken (purge, blocks, resets, EDR steps)
- Final verdict and rationale

### 4.2 Improvements

- Detection tuning opportunities (what would have caught it earlier?)
- Playbook updates (any missing steps or unclear hand-offs?)
- Awareness/training notes (common user pitfalls observed)

---

## Roles & Ownership (adapt to your environment)

- **SOC L1:** intake, triage, enrichment, documentation, escalation, coordination
- **Messaging / Email Security:** quarantine, purge, sender/URL blocks, mail flow rules
- **IAM:** password resets, session revocation, MFA resets, account disable
- **Endpoint / EDR:** isolate host, scans, triage collection, deeper endpoint investigation
- **IR / SOC L2+ :** confirmed compromise handling, forensics, incident management

---

## Completion Criteria ("Done" means)

- [ ] Verdict recorded with supporting evidence
- [ ] Containment performed (or requested) and confirmed
- [ ] Impacted users identified and notified (as required)
- [ ] Credentials/session actions completed if needed
- [ ] Evidence captured and stored
- [ ] Follow-up monitoring set (where relevant)
- [ ] Detection tuning request logged (if applicable)
