# AI Agent — Automated QA Test Case Generator

An AI agent that autonomously reads a Jira ticket, reasons over the requirements, and generates a complete structured test suite — exported to Google Sheets in under a minute.

No manual test case writing. Just submit a ticket ID.

Built with n8n, Google Gemini, and Google Sheets.

### n8n Workflow
![n8n Workflow](assets/workflow.png)

---

## What It Does

This is an AI agent — it doesn't just respond to prompts, it acts autonomously:

1. **Ingests** a Jira ticket ID from a form submission
2. **Fetches** the ticket from Jira — summary, description, acceptance criteria
3. **Reasons** over the requirements using Google Gemini — detects feature type, infers module, adjusts depth based on priority
4. **Generates** a structured test suite with no human input
5. **Writes** results to Google Sheets — two tabs ready for immediate use

The agent runs the full pipeline end-to-end. You submit a ticket ID. You get a test suite.

---

## Pipeline Architecture

```
Ingest          Extract            Generate           Transform                    Load
─────────────────────────────────────────────────────────────────────────────────────────
User Input  →  Jira Ticket  →  AI Test Generation  →  Parse + Aggregate + Map  →  Google Sheets
(Form)         (Jira API)      (Google Gemini)         (Clean + Format)            (2 tabs)
```

**Pipeline stages:**
- **Data Extract** — fetches raw ticket data from Jira
- **Data Generation** — AI enriches the data into structured test cases
- **Data Transformation** — cleans, parses, and maps the AI output
- **Data Load** — writes to Google Sheets (Test Cases + Execution Tracker tabs)

---

## Adaptive Test Generation

The AI adjusts depth and focus based on ticket content:

| Priority | Test Cases Generated |
|---|---|
| High | 20 — extensive edge cases and boundary conditions |
| Medium | 15 — standard coverage |
| Low | 10 — happy path and key negative cases |

Feature type is auto-detected from the ticket:
- **UI** → form validation, navigation, error states, empty states
- **API** → status codes, payloads, authentication, rate limits
- **Mobile** → touch interactions, orientation, offline mode
- **Accessibility** → screen reader, keyboard navigation, contrast

---

## Tech Stack

| Tool | Purpose |
|---|---|
| [n8n](https://n8n.io) | Workflow automation |
| [Google Gemini](https://ai.google.dev) | AI test case generation |
| [Jira Cloud](https://www.atlassian.com/software/jira) | Ticket source |
| [Google Sheets](https://sheets.google.com) | Output destination |

---

## Prerequisites

- n8n instance (cloud or self-hosted)
- Jira Cloud account + API token
- Google account with Sheets access (OAuth2)
- Google Gemini API key

---

## Setup

1. **Import the workflow**
   - In n8n, go to Workflows → Import
   - Upload `QA Management Tool Suite.json`

2. **Set up credentials**
   - `Jira SW Cloud` — add your Jira domain, email, and API token
   - `Google Sheets OAuth2` — connect your Google account
   - `Google Gemini` — add your Gemini API key

3. **Activate the workflow**
   - Click Activate in n8n
   - Open the form URL and submit a Jira ticket ID

---

## Output

Each run creates a new Google Sheet named `{TICKET-ID} Test Cases Report` with:

**Tab 1 — Test Cases**
| Field | Description |
|---|---|
| Test Case ID | TC-{TICKETID}-001 format |
| Title | Action-based test case name |
| Module | Auto-detected from ticket |
| Suite | Smoke / Functional / Regression / Edge Case |
| Preconditions | System and data state required |
| Test Steps | Numbered steps |
| Expected Result | Observable outcome |
| Test Data | Specific inputs needed |
| Test Type | Positive / Negative / Edge Case |
| Priority | High / Medium / Low |
| Status | Not Run |

**Tab 2 — Execution Tracker**
All test case fields plus:
- Run Date
- Actual Result
- Execution Status (Not Run / Pass / Fail / Blocked / Skipped)

---

## Roadmap

- [ ] GitHub Issues support
- [ ] Azure DevOps support
- [ ] Linear support
- [ ] TestRail output
- [ ] Observable dashboard — coverage metrics, pass/fail trends, sprint reports
- [ ] Slack notification on completion
- [ ] Multiple ticket processing in one run

---

## Contributing

This project is actively being developed. PRs and suggestions welcome.

If you add support for a new ticket system (Linear, GitHub Issues, Azure DevOps) or a new output (TestRail, Jira Xray), open a PR — it will be merged.

---

## Built By

Sakshi Patil — QA Engineer  
[LinkedIn](https://www.linkedin.com/in/sakshi-patil-50b0b8205/) | [GitHub](https://github.com/sakshiipatiil)
