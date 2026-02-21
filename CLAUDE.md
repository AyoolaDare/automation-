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
