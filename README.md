# 🛠️ EGU Jira Skill — `egu-jira`

A Claude skill for creating Jira issues in the **EG Utility Cloud (EGU)** project. It enables fast, consistent story/bug/task creation with all required EGU-specific fields auto-filled — including support for Figma design context.

---

## 📦 What It Does

- Creates **Stories, Bugs, and Tasks** in the EGU Jira project
- Auto-fills all EGU-specific custom fields (Team, AI-generated, Labels, Security, etc.)
- Generates structured descriptions using Jira wiki markup templates
- Supports **Figma design integration** — fetch a design and generate a story from it
- Links issues to Epics after creation
- Shows a draft for confirmation before creating the ticket

---

## 🚀 Usage

### Via command:
```
/egu-jira Story Add Login Page to SSP
/egu-jira Bug Login fails on SSP
/egu-jira Task Update dependencies
```

### Via natural language:
Just describe a feature — the agent treats it as a story creation request automatically.

```
"Create a login user story for SSP"
"We need a dashboard page for consumption tracking"
```

### With Figma design:
```
"Fetch the design from this Figma URL and create a user story"
```

---

## 📋 Supported Issue Types

| Type | Issue Category | Threat Risk |
|------|---------------|-------------|
| Story | New features and functionality | 0 - None |
| Bug | Maintenance and defects | 1 - Low |
| Task | Technical debt | 0 - None |

---

## 🔧 Auto-filled Fields

These fields are always set automatically — you never need to provide them:

| Field | Value |
|-------|-------|
| Project | `EGU` |
| Team | `Team Nova` |
| AI-generated | `Yes` |
| Labels | `AI_Story` |
| Change Type | `1 - Normal` |
| Data Classification | `2 - Normal data - INTERNAL` |
| Security Level | `ALL EG` (default) |

---

## ❓ Fields You'll Be Asked For

| Field | Required | Notes |
|-------|----------|-------|
| Summary | ✅ Yes | Pre-filled from your command/description |
| Priority | ✅ Yes | Highest / High / Medium / Low / Lowest |
| Epic Link | ❌ Optional | Format: `EGU-#####` |
| Component | ❌ Optional | `Customer Self-service Portal for B2C and SME` or `Partner Portal` |
| Security Level | ❌ Conditional | Only asked if attachments or personal data are mentioned |

---

## 📝 Description Templates

### Story
```
h2. User Story
h2. Business Context
h2. Feature Description
h2. User Flow
h2. UI/UX Requirements
h2. Acceptance Criteria
h2. Out of Scope
```

### Bug
```
h2. Problem
h2. Affected Files
h2. Impact
h2. Suggested Fix
```

### Task
```
h2. Description
h2. Scope
h2. Done When
```

---

## 🔗 Ticket URL Format

```
https://jira.eg.dk/browse/EGU-#####
```

---

## ⚠️ Important Notes

- **No Subtasks** — EGU does not use the Subtask issue type
- **No ERP Activity** — not used in EGU, never ask or include
- **Custom field format** — SELECT fields are passed as plain strings, not `{"value": "..."}` objects
- **Epic linking** — done via `jira_link_to_epic` after issue creation, not as a parent field
- **Wiki markup** — descriptions use Jira wiki markup, not Markdown

---

## 📁 Skill Location

```
https://raw.githubusercontent.com/Harshitha-Nag/Jira/main/SKILL.md
```
