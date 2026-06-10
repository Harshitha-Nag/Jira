# EGU Claude Slash Commands

AI-powered slash commands for the **EG Utility Cloud (EGU)** project. These templates are used with Claude to streamline Jira issue creation and client release note generation.

---

## Commands

### `/egu-jira` — Create a Jira Issue

Creates a Bug, Story, or Task in the EGU Jira project with all required fields pre-filled.

**Usage**

```
/egu-jira [issue-type] [summary]
```

**Examples**

```
/egu-jira Bug Login fails on SSP
/egu-jira Story Add Login Page to SSP
/egu-jira Task Update dependencies
```

**What it does**

- Prompts for description, priority, and optional epic link
- Auto-fills team (`Team Nova`), labels (`AI_Story`), data classification, change type, and threat risk
- Formats the description using Jira wiki markup templates for Bug / Story / Task
- Links the issue to an epic if provided
- Returns the created issue key and URL

**Auto-filled fields by issue type**

| Field | Bug | Story | Task |
|---|---|---|---|
| Issue Category | Maintenance and defects | New features and functionality | Technical debt |
| Threat Risk | 1 - Low | 0 - None | 0 - None |
| Team Name | Team Nova | Team Nova | Team Nova |
| AI-generated | Yes | Yes | Yes |
| Labels | AI_Story | AI_Story | AI_Story |
| Security Level | ALL EG | ALL EG | ALL EG |

---

### `/egu-release-notes` — Generate Client Release Notes

Fetches a Jira story, generates formal client-facing release notes, and updates the ticket.

**Usage**

```
/egu-release-notes [jira-issue-key]
```

**Examples**

```
/egu-release-notes EGU-3191
/egu-release-notes EGU-1234
```

**What it does**

- Fetches the story summary and description from Jira
- Generates structured, client-friendly release notes in EGU format
- Shows a draft for review before updating
- Updates `Client Release Notes` (customfield_10200) on the ticket
- Sets `Include in client release notes` to `Yes` (customfield_10211)

**Release notes sections**

Every generated document includes: Overview, What's New, User Access & Permissions, How It Works, Key Benefits, Important Notes, and Impact. Sections not applicable are marked `N/A`.

---

## Project Details

| Setting | Value |
|---|---|
| Jira Project | EGU (EG Utility Cloud) |
| Issue URL pattern | `https://jira.eg.dk/browse/EGU-#####` |
| Default team | Team Nova |
| Default security level | ALL EG |
| Labels always applied | `AI_Story` |
| AI-generated field | Always set to `Yes` |

---

## Notes

- **No subtasks** — EGU does not use the Subtask issue type.
- **Components** — Optional. Valid values: `Customer Self-service Portal for B2C and SME` or `Partner Portal`. Only included when relevant.
- **Security level** — Defaults to `ALL EG`. Escalated to `EU GDPR` only when attachments or personal data are involved.
- **Custom fields** — All select fields are passed as plain strings, not `{"value": "..."}` objects.
- **Release notes** — A draft is always shown for confirmation before the Jira ticket is updated.
