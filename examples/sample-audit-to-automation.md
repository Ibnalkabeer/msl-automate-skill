# Example Walkthrough: Turning an Audit Report into a Verified Workflow with `/automate`

## User Input
```text
/automate Our digital agency loses 40% of inbound webform leads because account managers take 6+ hours to copy leads from WordPress forms into Google Sheets and message them on WhatsApp. They also want an AI to write a 5-page proposal for every lead before anyone talks to them.
```

---

## `/automate` Output

### Stage 1: 5-Element Process Map & Step Deletion Gate

1. **5-Element Map:**
   - **Trigger:** WordPress / Webform `POST` webhook on form submission.
   - **Data Sources:** Incoming form payload (`name`, `email`, `phone`, `company`, `budget`, `notes`).
   - **Data Transformations:** Normalize phone number to `E.164` (`+234...`), generate 1-click `https://wa.me/...` link, lowercase email.
   - **Decision Points:** Score urgency (`HIGH | MEDIUM | LOW`) from budget and inquiry notes.
   - **Destination:** Upsert row to Google Sheets CRM + send instant WhatsApp/Slack alert with a 1-click pre-filled WhatsApp reply link.
2. **Elon's Deletion Gate (Eliminate First):**
   - **DELETED:** *"Have AI auto-generate a 5-page proposal before talking to the lead."* Sending a 5-page AI proposal to an unqualified webform lead before a 15-minute discovery call is waste that lowers reply rates. Replace with a 1-sentence personalized WhatsApp opener.
3. **Target KPI (Bucket 1 — Get More Customers):**
   - Cut lead response time from **6+ hours to under 60 seconds** and lift discovery call booking rate.

---

### Stage 2: The 1-3-1 Engine Router (n8n vs. AI Agent)

| Option | Autonomy | Architecture | Verdict |
| :--- | :---: | :--- | :--- |
| **Option 1: Pure n8n** | **L1** | Webhook -> E.164 Sanitizer -> IF Budget > Threshold -> Sheet + WhatsApp Alert. | Fast, but cannot summarize messy free-text inquiry notes. |
| **Option 2: n8n + Isolated AI Node** | **L2** | Deterministic n8n pipeline + **1 isolated LLM node** (`temperature: 0.1`, `continueOnFail: true`) to score urgency and draft a 1-line WhatsApp opener. | **RECOMMENDED** — 85% deterministic plumbing with zero risk of a dropped lead if the LLM times out. |
| **Option 3: Stateful AI Agent** | **L3** | Full conversational WhatsApp bot handling multi-turn discovery. | Overkill for Day 1; introduces hallucination risk before baseline speed-to-lead is fixed. |

---

### Stage 3: Assembled Artifact
- Generated from [`templates/starter-speed-to-lead.n8n.json`](../templates/starter-speed-to-lead.n8n.json) with E.164 sanitization, `continueOnFail: true` on the AI classifier, and the `99_Error_Circuit_Breaker` node active.

---

### Stage 4: Lego Validation Chain & 14-Day Rollout
- [ ] **Block 1:** Fire sample `curl` payload to `01_Webhook_Lead_Ingress` and verify `phone_e164` and `whatsapp_url` in `02_Sanitize_E164_And_Email`.
- [ ] **Block 2:** Run `03_Isolated_AI_Lead_Classifier` with a valid API key and once with an invalid key to verify `continueOnFail` still routes the lead.
- [ ] **Block 3:** Confirm the alert lands in Slack/WhatsApp in `<60 seconds` with a working 1-click `https://wa.me/` link.
- [ ] **Block 4:** Trigger `99_Error_Circuit_Breaker` to confirm failure alerts arrive immediately.
- **Kill Switch:** Toggle the workflow `Inactive` in n8n top-right bar to revert to native form email notifications in 5 seconds.
