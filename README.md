<div align="center">

# AI QA Management Suite

**An autonomous AI agent that reads a Jira ticket or GitHub Issue, generates a complete test suite, writes executable test scripts, and commits them to GitHub — all in under 2 minutes.**

No manual test case writing. No script authoring. No copy-paste. Just submit a ticket ID.

[![n8n](https://img.shields.io/badge/Built%20with-n8n-EF6C35?style=flat-square&logo=n8n&logoColor=white)](https://n8n.io)
[![Gemini](https://img.shields.io/badge/Test%20Cases-Google%20Gemini%202.5-4285F4?style=flat-square&logo=google&logoColor=white)](https://ai.google.dev)
[![OpenRouter](https://img.shields.io/badge/Scripts-OpenRouter%20LLM-6C47FF?style=flat-square)](https://openrouter.ai)
[![Sheets](https://img.shields.io/badge/Output-Google%20Sheets-34A853?style=flat-square&logo=googlesheets&logoColor=white)](https://sheets.google.com)
[![GitHub](https://img.shields.io/badge/Committed%20to-GitHub-181717?style=flat-square&logo=github&logoColor=white)](https://github.com)

![Workflow](assets/workflow.png)

</div>

---

## What It Does

Submit a Jira ticket ID or GitHub Issue number. The agent runs end-to-end:

**Phase 1 — Test Suite Generation**

1. **Fetches** the full ticket — summary, description, priority, acceptance criteria
2. **Reasons** over the requirements using Google Gemini 2.5 Flash
3. **Generates** 10–20 structured test cases scaled to ticket priority
4. **Exports** to Google Sheets — Test Cases tab + Execution Tracker tab, pre-filled
5. **Posts** the sheet link as a comment on the original Jira ticket or GitHub issue

**Phase 2 — Test Script Generation**

6. **Prompts** for your testing framework (Playwright, Selenium, Cypress, etc.)
7. **Generates** executable test scripts via OpenRouter LLM — written for your stack, not a generic one
8. **Commits** the scripts directly to your GitHub repository — files created or updated automatically

The agent handles everything between ticket and committed test scripts.

---

## Full Pipeline

```
┌───────────┐   ┌────────────────┐   ┌───────────────┐   ┌──────────────┐   ┌───────────────┐   ┌────────────┐
│   Form    │──▶│  Jira / GitHub │──▶│ Google Gemini │──▶│  Parse +     │──▶│ Google Sheets │──▶│ Comment on │
│   Input   │   │      API       │   │  2.5 Flash    │   │  Aggregate   │   │   (2 tabs)    │   │   ticket   │
└───────────┘   └────────────────┘   └───────────────┘   └──────────────┘   └───────────────┘   └────────────┘
                                                                                      │
                                                                                      ▼
                                                                          ┌───────────────────┐
                                                                          │  Framework Input  │
                                                                          │   (Form Page 2)   │
                                                                          └─────────┬─────────┘
                                                                                    │
                                                              ┌─────────────────────▼──────────────────────┐
                                                              │          OpenRouter LLM                    │
                                                              │       (Test Script Generation)             │
                                                              └─────────────────────┬──────────────────────┘
                                                                                    │
                                                                          ┌─────────▼─────────┐
                                                                          │   HTTP Request    │
                                                                          │   GitHub API      │
                                                                          └─────────┬─────────┘
                                                                                    │
                                                              ┌─────────────────────▼──────────────────────┐
                                                              │         Create file  /  Edit file          │
                                                              │              (GitHub repo)                 │
                                                              └────────────────────────────────────────────┘
```

**Pipeline stages:**

| Stage | What happens |
|---|---|
| Data Extract | Switch node routes to Jira or GitHub API based on input type, fetches the full issue |
| Data Generation | Gemini 2.5 Flash reasons over requirements and generates structured test cases |
| Data Transformation | Parses, cleans, and maps AI output into a consistent schema |
| Data Load | Writes to Google Sheets — Test Cases tab + Execution Tracker tab |
| Post Back | Comments the sheet link on the original ticket |
| Framework Input | Second form page prompts for the target testing framework |
| Script Generation | OpenRouter LLM generates executable test scripts for the chosen framework |
| GitHub Commit | HTTP request creates or updates files directly in the target repo |

---

## Adaptive Test Generation

**By priority:**

| Priority | Test Cases | Coverage |
|---|---|---|
| High | 20 | Extensive edge cases, boundary conditions, negative paths |
| Medium | 15 | Standard functional coverage |
| Low | 10 | Happy path + key negative scenarios |

**By feature type (auto-detected from ticket):**

| Type | Focus Areas |
|---|---|
| UI | Form validation, navigation, error states, empty states |
| API | Status codes, payloads, authentication, rate limits |
| Mobile | Touch interactions, orientation changes, offline mode |
| Accessibility | Screen reader, keyboard navigation, colour contrast |

---

## Output

### Google Sheets — `{TICKET-ID} Test Cases Report`

**Tab 1 — Test Cases**

| Field | Description |
|---|---|
| Test Case ID | `TC-{TICKETID}-001` format |
| Title | Action-based test case name |
| Module | Auto-detected from ticket |
| Suite | Smoke / Functional / Regression / Edge Case |
| Preconditions | System and data state required |
| Test Steps | Numbered, clear steps |
| Expected Result | Observable, verifiable outcome |
| Test Data | Specific inputs required |
| Test Type | Positive / Negative / Edge Case |
| Priority | High / Medium / Low |
| Status | Not Run |

**Tab 2 — Execution Tracker**

All fields from Tab 1, plus:

| Field | Options |
|---|---|
| Run Date | — |
| Actual Result | — |
| Execution Status | Not Run / Pass / Fail / Blocked / Skipped |

---

### GitHub — Test Scripts

Scripts are committed directly to your repo under the target path you specify.

**Example output (Playwright):**

```javascript
test('Login with valid credentials', async ({ page }) => {
  await page.goto('/login');
  await page.fill('#email', 'user@example.com');
  await page.fill('#password', 'ValidPass123');
  await page.click('[type=submit]');
  await expect(page).toHaveURL('/dashboard');
});

test('Login with invalid password returns error', async ({ page }) => {
  await page.goto('/login');
  await page.fill('#email', 'user@example.com');
  await page.fill('#password', 'wrongpassword');
  await page.click('[type=submit]');
  await expect(page.locator('.error-message')).toBeVisible();
});
```

Scripts are structured, named, and ready to run — not boilerplate. The LLM uses the test cases from Phase 1 as input, so scripts map directly to the generated test suite.

**Supported frameworks (via framework input form):**
- Playwright
- Selenium (Python / Java / JavaScript)
- Cypress
- WebdriverIO
- pytest
- JUnit
- Any framework — the LLM adapts to whatever you specify

---

## Tech Stack

| Tool | Purpose |
|---|---|
| [n8n](https://n8n.io) | Workflow orchestration |
| [Google Gemini 2.5 Flash](https://ai.google.dev) | Test case generation |
| [OpenRouter](https://openrouter.ai) | Test script generation |
| [Jira Cloud](https://www.atlassian.com/software/jira) | Ticket source |
| [GitHub Issues](https://github.com) | Ticket source |
| [Google Sheets](https://sheets.google.com) | Test suite output |
| [GitHub API](https://docs.github.com/en/rest) | Script commit destination |

---

## Prerequisites

- n8n instance (cloud or self-hosted)
- Jira Cloud account + API token
- GitHub account + personal access token (with `repo` scope)
- Google account with Sheets access (OAuth2)
- Google Gemini API key
- OpenRouter API key

---

## Setup

**1. Import the workflow**

```
n8n → Workflows → Import → upload workflow/ai-qa-management-suite.json
```

**2. Add credentials**

| Credential | Required fields |
|---|---|
| Jira SW Cloud | Domain, email, API token |
| GitHub | Personal access token (`repo` scope) |
| Google Sheets OAuth2 | Google account (OAuth flow) |
| Google Gemini | API key |
| OpenRouter | API key |

**3. Configure GitHub target**

In the HTTP Request node (Script Generation stage), set:
- `owner` — your GitHub username
- `repo` — target repository name
- `path` — folder where scripts should be written (e.g. `tests/`)

**4. Activate and run**

- Click **Activate** in n8n
- Open the form URL
- Submit a Jira ticket ID or GitHub Issue number
- On the second form page, enter your testing framework
- Check your ticket for the auto-posted test suite comment
- Check your GitHub repo for the committed test scripts

---

## Roadmap

- [x] Jira Cloud support
- [x] GitHub Issues support
- [x] Google Sheets export (Test Cases + Execution Tracker)
- [x] Auto-comment on ticket
- [x] Test script generation
- [x] GitHub commit via API
- [ ] Azure DevOps support
- [ ] Linear support
- [ ] TestRail export
- [ ] Jira Xray export
- [ ] Slack notification on completion
- [ ] Batch processing — multiple tickets in one run
- [ ] Coverage dashboard — metrics, pass/fail trends, sprint reports

---

## Contributing

PRs and suggestions welcome.

Adding support for a new ticket source (Linear, Azure DevOps), output format (TestRail, Jira Xray), or testing framework? Open a PR — it will be reviewed and merged.

---

<div align="center">

Built by **Sakshi Patil** — QA Engineer

[LinkedIn](https://www.linkedin.com/in/sakshi-patil-50b0b8205/) · [GitHub](https://github.com/sakshiipatiil)

*If this saved you time, drop a ⭐*

</div>
