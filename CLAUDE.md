# n8n Skills Guide (skills.md)

A practical, copy-paste-friendly guide for building reliable automations in **n8n**.

---

## What is n8n (in one line)
n8n is like a visual “if this happens, do that” builder where you connect apps (Google, email, databases, WhatsApp providers, etc.) into workflows.

---

## Core concepts you must know

### 1) Workflow
A workflow is the whole automation, from trigger to final action.

### 2) Trigger node
The “start button.” Examples:
- **Webhook Trigger** (best for forms and custom apps)
- **Cron** (runs on a schedule)
- **Google Sheets Trigger** (changes in a sheet)
- **Gmail Trigger** (new email)

### 3) Nodes
Each step is a node: read data, transform, branch, send message, write to a sheet, etc.

### 4) Items
n8n processes data as **items** (like rows). Each node usually receives items and outputs items.

### 5) Expressions
You can pull data from previous nodes using expressions like:
- `{{$json.name}}`
- `{{$json.email}}`

---

## Recommended workflow design patterns

### Pattern A: Webhook intake → Validate → Store → Respond
Use this when data comes from a form or website.

**Best nodes:**
- Webhook
- Set / Function / Code
- IF
- Google Sheets / Database
- Email / SMS / WhatsApp provider

### Pattern B: Daily scheduler → Query due reminders → Send → Mark as sent
Use for reminders and timed follow-ups.

**Best nodes:**
- Cron
- Google Sheets (Read) / DB query
- IF / Filter
- Send message
- Update row/status

### Pattern C: State machine with “Status” column
Keep a `status` field like:
- `NEW`
- `WELCOME_SENT`
- `REMINDER_SENT`
- `PAID`
- `CONFIRMED`

This prevents duplicates and makes debugging easy.

---

## Data model for donor automation (suggested)
Store each donor record with:

- `donor_id` (unique)
- `full_name`
- `phone`
- `email`
- `sponsorship_type`
- `expected_amount`
- `expected_date`
- `reminder_days_before` (e.g., 3)
- `channel` (email/whatsapp/sms)
- `status`
- `welcome_sent_at`
- `reminder_sent_at`
- `paid_amount`
- `paid_date`
- `confirmed_sent_at`

Where to store:
- **Google Sheets** (simple, quick)
- **Airtable** (nice UI)
- **Postgres/MySQL** (best long-term)
- **n8n Data Store** (okay for small setups)

---

## Nodes you’ll use a lot

### Data + logic
- **Set**: rename fields, build clean JSON
- **IF**: branching logic
- **Merge**: combine streams
- **Split in Batches**: process many rows safely
- **Wait**: delay a workflow (works, but Cron-based reminders scale better)
- **Code**: custom transforms (keep it minimal)

### Storage
- Google Sheets
- Airtable
- PostgreSQL / MySQL
- Redis (advanced)

### Messaging
- Email (SMTP / Gmail)
- SMS (Twilio or similar)
- WhatsApp (via approved providers like Twilio/Meta BSP)
  - Note: WhatsApp usually requires templates for certain message types.

---

## Building your donor automation (3 workflows)

You want:
1) Collect donor details
2) Send welcome immediately
3) Send reminder near expected date
4) Send confirmation after payment

Best practice: split into **3 workflows** so each is clean and reliable.

---

## Workflow 1: Donor intake + welcome

### Goal
When a donor submits the embedded form:
- validate details
- save to Sheet/DB
- send welcome + thank you
- mark status

### Flow (ASCII)


### Scaling tip
Use **Split in Batches** so you don’t hit rate limits sending lots of messages.

---

## Workflow 3: Payment confirmation

### Goal
When finance confirms payment (via form, admin panel, or updating a sheet):
- verify record
- send confirmation receipt message
- mark confirmed

### Good triggers
- **Webhook** (finance submits a “payment confirmation” form)
- **Google Sheets Trigger** (row updated to `PAID`)
- **Manual trigger** (small churches sometimes start here)

### Flow (ASCII)



---

## Reliability checklist (avoid common headaches)

### Deduplication
Always store and check:
- `welcome_sent_at`
- `reminder_sent_at`
- `confirmed_sent_at`

Never “just send.” Always check first.

### Timezones
Set n8n timezone to match your church location.
When comparing dates, compare **date-only** (not full timestamp) if your rule is “3 days before”.

### Rate limits
If sending many messages:
- Split in Batches (e.g., 25 at a time)
- Add small Wait between batches (2–5 seconds)

### Error handling
Use:
- “Continue On Fail” where safe
- Separate error branch that notifies an admin (Email/Telegram)

### Logging
Write a log entry for each send:
- who
- what message type
- timestamp
- result (success/fail)
- error text

---

## Message templates (copy/paste)

### Welcome
Hello {{name}},
Thank you for sponsoring {{sponsorship_type}}.
Expected donation: {{expected_amount}} on {{expected_date}}.
God bless you.

### Reminder
Hello {{name}},
A gentle reminder: your sponsorship donation of {{expected_amount}} is due on {{expected_date}}.
Thank you for your support. God bless you.

### Confirmation
Hello {{name}},
We confirm receipt of your donation of {{paid_amount}} on {{paid_date}} for {{sponsorship_type}}.
Thank you. God bless you.

---

## Security basics
- Don’t expose admin webhooks publicly without a secret token.
- Use environment variables for API keys (don’t paste keys into nodes).
- Limit who can edit workflows in n8n.
- Back up your n8n data regularly.

---

## Quick start: recommended stack
- n8n (self-hosted or cloud)
- Google Sheets (start simple)
- Email via SMTP/Gmail
- Optional: WhatsApp via an approved provider later

---

## Done definition (your automation is “complete” when)
- New donor submissions always create a record
- Welcome message sends once only
- Reminders send on correct days, no duplicates
- Payment confirmation sends only after payment is marked
- Errors alert an admin and are visible in logs
