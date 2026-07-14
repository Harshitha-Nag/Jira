name: egu-jira
argument-hint: [issue-type] [summary]
description: Create a Jira issue (Bug, Story, Task, Spike) in EGU (EG Utility Cloud) project. Use when creating bugs, creating Spike, filing stories, or making tasks in the EGU project.

Create a Jira issue in the EGU (EG Utility Cloud) project.

**Arguments**: $ARGUMENTS

## Usage Examples
- `/jira Bug Login fails on SSP`
- `/jira Story Add Login Page to SSP`
- `/jira Task Update dependencies`
- `/jira Spike to investigate`

## Workflow

### 1. Parse Arguments
Extract issue type and summary from arguments:
- First word should be issue type (Bug, Story, Task, Spike)
- Remaining text is the summary
- If no type specified, ask using AskUserQuestion
- If no summary, ask using AskUserQuestion

### 2. Gather All Details (Full Form)
Use AskUserQuestion to collect all fields upfront:

**Always ask for:**
- Summary (pre-fill from args if provided)
- Description (problem/requirements, impact, affected files)
- Priority (Highest, High, Medium, Low, Lowest, Not prioritized)
- Epic link (optional — EGU-##### format)

**Do NOT ask for:**
- Component — ask the user to pick from: `Customer Self-service Portal for B2C and SME`, `Partner Portal`. If neither fits, omit entirely.
- ERP Activity — manually updated in EGU
- Team — always auto-filled as `Team Nova`
- AI-generated — always auto-filled as `Yes`

### 2a. Security Level (Conditional)
**Ask about Security Level if** the user mentions any of these:
- Screenshots, images, attachments
- Customer data, personal information
- Screen recordings, logs with user data

Use AskUserQuestion with options:
- **ALL EG** (Recommended) — Standard visibility, no GDPR sensitive data
- **EU GDPR** — Contains potential GDPR sensitive data

**Default to ALL EG** (without asking) if no attachments or sensitive data are mentioned.

### 3. Load and Use Jira Tools
Load the required tools using ToolSearch:
```
ToolSearch: select:mcp__mcp-jira-service__jira_create_issue
ToolSearch: select:mcp__mcp-jira-service__jira_link_to_epic
```

### 4. Create Issue
Use `mcp__mcp-jira-service__jira_create_issue` with:

**For Bug/Story/Task/Spike:**
```json
{
  "project_key": "EGU",
  "issue_type": "[Bug|Story|Task|Spike]",
  "summary": "[User-provided summary]",
  "description": "[User-provided description in Jira wiki markup]",
  "additional_fields": {
    "customfield_10213": "[Issue Category — see defaults below]",
    "customfield_11200": "1 - Normal",
    "customfield_11201": "2 - Normal data - INTERNAL",
    "customfield_10210": "[Threat Risk — see defaults below]",
    "customfield_10216": "Team Nova",
    "customfield_14600": "Yes",
    "labels": ["AI_Story"],
    "priority": { "name": "[User-selected priority]" },
    "security": { "name": "ALL EG" }
  }
}
```

> **Important — field format**: Pass all custom SELECT fields as plain strings (not `{"value": "..."}` objects).
> Only `priority` and `security` use object format. Passing `{"value": "..."}` for custom fields will cause a
> "Could not find valid id or value" error.

### 5. Link to Epic (if provided)
Use `mcp__mcp-jira-service__jira_link_to_epic`:
```json
{
  "issue_key": "[Created issue key]",
  "epic_key": "[User-provided epic key]"
}
```

### 6. Report Result
Show:
- Created issue key (e.g., EGU-12345)
- Issue URL: `https://jira.eg.dk/browse/EGU-#####`
- Summary of what was created

---

## Auto-filled Fields by Issue Type

### Bug
| Field | Field ID | Value |
|-------|----------|-------|
| Issue Category | customfield_10213 | `Maintenance and defects` |
| Change Type | customfield_11200 | `1 - Normal` |
| Data Classification | customfield_11201 | `2 - Normal data - INTERNAL` |
| Threat Risk | customfield_10210 | `1 - Low` |
| Team Name | customfield_10216 | `Team Nova` |
| AI-generated | customfield_14600 | `Yes` |
| Labels | labels | `Claude` |
| Security Level | security | `ALL EG` |

### Story
| Field | Field ID | Value |
|-------|----------|-------|
| Issue Category | customfield_10213 | `New features and functionality` |
| Change Type | customfield_11200 | `1 - Normal` |
| Data Classification | customfield_11201 | `2 - Normal data - INTERNAL` |
| Threat Risk | customfield_10210 | `0 - None` |
| Team Name | customfield_10216 | `Team Nova` |
| AI-generated | customfield_14600 | `Yes` |
| Labels | labels | `Claude` |
| Security Level | security | `ALL EG` |

### Task
| Field | Field ID | Value |
|-------|----------|-------|
| Issue Category | customfield_10213 | `Technical debt` |
| Change Type | customfield_11200 | `1 - Normal` |
| Data Classification | customfield_11201 | `2 - Normal data - INTERNAL` |
| Threat Risk | customfield_10210 | `0 - None` |
| Team Name | customfield_10216 | `Team Nova` |
| AI-generated | customfield_14600 | `Yes` |
| Labels | labels | `Claude` |
| Security Level | security | `ALL EG` |

### Spike
| Field | Field ID | Value |
|-------|----------|-------|
| Issue Category | customfield_10213 | `New features and functionality` |
| Change Type | customfield_11200 | `1 - Normal` |
| Data Classification | customfield_11201 | `2 - Normal data - INTERNAL` |
| Threat Risk | customfield_10210 | `0 - None` |
| Team Name | customfield_10216 | `Team Nova` |
| AI-generated | customfield_14600 | `Yes` |
| Labels | labels | `Claude` |
| Security Level | security | `ALL EG` |

---

## Valid Field Values (EGU-specific)

### Issue Category (customfield_10213)
- `New features and functionality`
- `Legal requirement`
- `Customer funded development`
- `Maintenance and defects`
- `Technical debt`
- `Rework`
- `Internal company work`

### Change Type (customfield_11200)
- `1 - Normal`
- `2 - Standard`
- `3 - Emergency`

### Data Classification (customfield_11201)
- `1 - Normal data - PUBLIC`
- `2 - Normal data - INTERNAL`
- `3 - Normal data - CONFIDENTIAL`
- `4 - Personal data - RELATED`
- `5 - Personal data - SENSITIVE`

### Threat Risk (customfield_10210)
- `0 - None`
- `1 - Low`
- `2 - Medium`
- `3 - High`

### Priority
- `Highest`, `High`, `Medium`, `Low`, `Lowest`, `Not prioritized`

### Components (optional — ask user if relevant)
- `Customer Self-service Portal for B2C and SME`
- `Partner Portal`

### Security Level
- `ALL EG` (default — no GDPR sensitive data)

---

## Description Templates (Jira Wiki Markup)

Convert user input to Jira wiki markup.

### For Bug
```
h2. Problem
[What's wrong — from user input]

h2. Affected Files
* {{path/to/file1.cs}}
* {{path/to/file2.cs}}

h2. Impact
[Who is affected and how]

h2. Suggested Fix
# Step 1
# Step 2
```

### For Story
```
h2. User Story
As a [type of user],
I want [goal],
So that [benefit].

h2. Business Context
[Why this feature is needed]
[Business value]
[Problem being solved]

h2. Feature Description
[What needs to be built — detailed developer instructions]

h2. User Flow
1. User does X
2. System displays Y
3. User selects Z
4. System performs action

h2. UI/UX Requirements
* Page/Screen:
* Components:
* Validation Rules:
* Error Messages:
* Accessibility Requirements:
* Responsive Behaviour:

h2. Acceptance Criteria
* Criterion 1
* Criterion 2
* Criterion 3

h2. Out of Scope
[What is explicitly NOT included in this story]
```

### For Task
```
h2. Description
[What needs to be done]

h2. Scope
* Item 1
* Item 2

h2. Done When
[Completion criteria]
```

### For Spike
```
h2. Description
[What needs to be done]

h2. Scope
* Item 1
* Item 2

h2. Done When
[Completion criteria]
```


---

## Error Handling

If creation fails:
1. Check error message for missing required fields
2. Ensure all SELECT custom fields are passed as plain strings, not `{"value": "..."}` objects
3. Verify `priority` uses `{"name": "..."}` object format
4. Verify `security` uses `{"name": "ALL EG"}` object format
5. Verify epic key exists and belongs to EGU project
6. Report specific error to user with suggested fix

---

## Notes

- **Project key**: Always `EGU`
- **Issue URL**: `https://jira.eg.dk/browse/EGU-#####`
- **Component**: Optional — ask user only if relevant; valid values are `Customer Self-service Portal for B2C and SME` or `Partner Portal`
- **ERP Activity**: Not used in EGU — do not ask or include
- **Team Name**: Always `Team Nova` (customfield_10216) — never ask
- **Labels**: Always set to `["Claude"]` — never ask, always include
- **AI-generated**: Always `Yes` (customfield_14600) — never ask, always set
- **Epic linking**: Use `jira_link_to_epic` AFTER creation (not a parent field)
- **Security Level**: Default to `ALL EG`; ask only if attachments or sensitive data are mentioned
- **Wiki markup**: Jira uses wiki markup, not markdown
- **No Subtasks**: EGU does not use the Subtask issue type
- **Custom field format**: Pass SELECT fields as plain strings — `"customfield_10213": "Technical debt"` — NOT as `{"value": "Technical debt"}`
