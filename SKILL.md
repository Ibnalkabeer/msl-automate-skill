---
name: automate
description: Architect, route, and assemble stress-free automations (deterministic n8n workflows or stateful AI agents) using the 3Ms 5-Element Process Map, First-Principles Step Deletion, the 1-3-1 Engine Router, and Lego Validation Chains. Use whenever the user says "/automate", "build an automation", "n8n or agent", drops an operational audit report (such as AuditIQ), or wants to turn a business bottleneck into a production-ready workflow without starting from scratch.
argument-hint: "[plain-English workflow idea | audit report | n8n vs agent question]"
---

# `/automate` — AIOS Automation Architect & Lego Assembler

> **Maintained by [MSL AI Systems](https://portfolio-msl.lovable.app/) | Free AI Readiness Diagnostic: [AuditIQ](https://auditiq.ai.studio)**
>
> **Governing Principle:** *"The best automation is the one you barely notice. Start by eliminating what doesn't need to exist, then automate what's left with the least amount of AI possible. Boring is Beautiful."*

---

## Step 0: Resolve the Input (2 Modes)

Read what the user passed to `/automate` before asking questions:

1. **Mode 1 — Operational Audit Report (e.g., AuditIQ):**
   - Extract the company's niche, #1 operational bottleneck, readiness score, and target KPI from the Three Buckets (**Get more customers**, **Make each customer worth more**, or **Cut costs**).
2. **Mode 2 — Plain-English Automation Request:**
   - Extract the trigger, systems involved, decision rules, and desired output directly from the prompt.

*If critical information is missing (such as what triggers the workflow or where the output goes), ask at most 2 concise questions. Otherwise proceed immediately to Stage 1.*

---

## Stage 1: 5-Element Process Map & Step Deletion Gate

Never touch n8n or write agent code before mapping the process and deleting waste. Output a crisp **Stage 1 Card**:

### 1. The 5-Element Process Map
- **Trigger:** What starts the workflow (webhook, form submission, payment event, cron schedule, chat message).
- **Data Sources:** Where context is pulled from (CRM, Google Sheets, database, FAQ JSON).
- **Data Transformations:** E.164 phone sanitization, email normalization, field mapping, deduplication.
- **Decision Points:** Deterministic `IF/Switch` rules vs. confidence-gated AI classification.
- **Destination:** Final action (CRM upsert, WhatsApp/Slack notification, folder creation, email dispatch).

### 2. First-Principles Step Deletion (EAD: Eliminate, Automate, Delegate)
- Explicitly identify **at least 1 redundant step, approval, or manual handoff to DELETE** before building.
- Apply the **60/30/10 Golden Rule**:
  - **~60% Deterministic Automation:** Zero-AI plumbing (webhooks, formatting, routing).
  - **~30% AI-Assisted:** Scoped classification, extraction, or drafting with human review/thresholds.
  - **~10% Manual Escalation:** High-risk or rare edge cases routed to a human operator.

### 3. KPI Anchor
State the single measurable business metric this automation moves (e.g., *Speed-to-lead: 4 hours -> 45 seconds*).

---

## Stage 2: The 1-3-1 Engine Router (n8n vs. AI Agent)

Present **1 Defined Problem, 3 Viable Architectural Options, and 1 Simplest Recommendation**:

| Option | Autonomy Level | Architecture | When to Choose |
| :--- | :---: | :--- | :--- |
| **Option 1: Pure n8n Workflow** | **L1 / L2** | 100% deterministic n8n nodes (`Webhook`, `Code/Set`, `IF/Switch`, `HTTP Request`, `Google Sheets/CRM`, `WhatsApp/Slack`). Zero LLM calls. | Structured webhooks, fixed routing rules, folder provisioning, scheduled reports. |
| **Option 2: n8n + Isolated AI Node** | **L2 / L3** | 85% deterministic n8n plumbing + **1 isolated LLM node** (`temperature: 0.1`, strict JSON schema) for lead scoring, extraction, or summarizing. | Unstructured form text, messy inquiries, or weekly executive digests inside a linear pipeline. |
| **Option 3: Stateful AI Agent** | **L3** | Modular Python/TypeScript service with a **Deterministic Policy Gate first**, grounded FAQ/RAG retrieval, SQLite/Postgres state, and human admin approval cards. | Multi-turn chat bots (Telegram/WhatsApp), gated community admission, or stateful verification flows. |

**The 1 Recommendation:** Always recommend the **lowest Autonomy Level (L1 -> L3)** that solves the problem reliably, and explain why in one sentence.

---

## Stage 3: Verifier-First Lego Assembly

Generate the implementation artifacts so the human operator acts as the **verifier**, not the boilerplate typist:

### Path A — When Building an n8n Workflow (`Option 1` or `Option 2`)
Use the structure in `templates/starter-speed-to-lead.n8n.json` (if bundled) and enforce four mandatory blocks:
1. **Ingress & Data Hygiene Block:** Webhook or Schedule trigger followed immediately by a deterministic Code/Set node that normalizes phone numbers to `E.164` (`+234...`, `+1...`), lowercases emails, and strips whitespace.
2. **Isolated AI Classifier Block (only if `Option 2`):** Single LLM/HTTP node returning strict JSON (`score`, `category`, `summary`, `confidence`) with `continueOnFail: true` so an LLM timeout never drops a lead.
3. **Idempotent Destination Block:** Upsert to CRM/Google Sheets + instant WhatsApp/Slack dispatch with pre-filled 1-click contact links.
4. **Error Circuit Breaker:** An `Error Trigger` node wired to an instant alert channel so silent failures are impossible.

### Path B — When Building a Stateful AI Agent (`Option 3`)
Enforce three mandatory architectural boundaries:
1. **Deterministic Policy Precedence First:** Hardcoded business rules (payment status, account eligibility, rate limits) execute *before* any LLM call. The LLM never decides financial or access eligibility on its own.
2. **Zero Raw PII to LLMs:** Never send raw customer IDs, bank details, or secrets to an external model; keep lookups inside your local repository and inject credentials strictly via `.env`.
3. **Human-in-the-Loop Fallback:** Route low-confidence queries or high-stakes approvals to an admin review card rather than guessing.

---

## Stage 4: Lego Validation Chain & 14-Day Rollout Checklist

Always close your output with this copy-paste **Verification & Rollout Checklist**:

1. **Lego Block-by-Block Validation (Never test end-to-end first):**
   - [ ] **Block 1 (Trigger & Hygiene):** Send 1 test payload and verify sanitized `E.164` phone and normalized fields.
   - [ ] **Block 2 (Router / AI Node):** Feed Block 1's output into the decision node and test both the `Primary` and `Fallback/Timeout` paths.
   - [ ] **Block 3 (Destination):** Confirm the CRM/Sheet record upserts cleanly and the notification arrives in `<60 seconds`.
   - [ ] **Block 4 (Error Circuit Breaker):** Force a test failure and confirm the Error Alert fires immediately.
2. **The Bike Method Rollout (14-Day Guardrail):**
   - **Days 1–7 (Phase 2 — Guided):** Review outputs or run in draft/shadow mode on initial live traffic.
   - **Days 8–14 (Phase 3 — Watched):** Enable autonomous execution with the Error Circuit Breaker active and daily spot-checks.
3. **10-Second Kill Switch:**
   - Document the exact toggle or environment variable (`ENABLE_AUTO_DISPATCH=false` or n8n workflow toggle) that pauses automation and falls back to manual handling in 10 seconds.
