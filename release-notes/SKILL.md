name: egu-release-notes
argument-hint: [jira-issue-key]
description: Generate and update Client Release Notes for an EGU Jira story. Fetches the story summary and description, generates formal release notes in the standard EGU format, shows a draft for confirmation, then updates the Jira ticket with the release notes and sets "Include in client release notes" to Yes.


Generate and publish Client Release Notes for an EGU Jira story.

**Arguments**: $ARGUMENTS

## Usage Examples
- `/egu-release-notes EGU-3191`
- `/egu-release-notes EGU-1234`
- "Create release notes for EGU-3191"
- "Generate release notes for the login story"

---

## Workflow

### 1. Ask for Jira Issue Key
If no issue key is provided in the arguments, ask the user:
- "Please provide the Jira story key (e.g. EGU-3191)"

### 2. Fetch the Jira Issue
Use `jira_get_issue` with `fields: "*all"` to retrieve:
- `summary` — the story title
- `description` — the full story description (User Story, Feature Description, Acceptance Criteria, etc.)

### 3. Generate Client Release Notes
Using the fetched summary and description, generate formal and professional release notes
following the **EGU Release Notes Format** defined below.

### 4. Show Draft and Ask for Confirmation
Display the generated release notes clearly and ask:
- "Does this look good, or would you like to edit the wording?"

Options:
- Yes, update the ticket
- Edit the release notes text

If the user selects "Edit", ask them to provide the updated text, then re-confirm before updating.

### 5. Update the Jira Issue
Use `jira_update_issue` with:

```json
{
  "issue_key": "[Issue Key]",
  "fields": {},
  "additional_fields": {
    "customfield_10200": "[Generated release notes text]",
    "customfield_10211": "Yes"
  }
}
```

**Field reference:**
| Field | Field ID | Value |
|-------|----------|-------|
| Client Release Notes | customfield_10200 | Generated release notes (plain text, no wiki markup) |
| Include in client release notes | customfield_10211 | `Yes` |

### 6. Report Result
Show:
- ✅ Updated issue key (e.g. EGU-3191)
- Issue URL: `https://jira.eg.dk/browse/EGU-#####`
- Confirmation that release notes were set and "Include in client release notes" was set to Yes

---

## EGU Release Notes Format

Always generate release notes using the following structure.
Each section must be present. If a section is not applicable, write "N/A" for that section.

```
h2. Release Notes – [Story Summary]

h2. Overview
[1–2 sentences describing what this release is about and which product/area it affects.]

h2. What's New
[Bullet points listing the new features or changes introduced. Focus on what the user sees and can do.]
- [New feature or change 1]
- [New feature or change 2]
- [New feature or change 3]

h2. User Access & Permissions
[Describe who can access this feature — all users, specific roles, authenticated users only, etc.]

h2. How It Works
[Step-by-step description of how the user interacts with the new feature. Written in plain language.]
1. [Step 1]
2. [Step 2]
3. [Step 3]

h2. Key Benefits
[Bullet points highlighting the value and benefits to the user.]
- [Benefit 1]
- [Benefit 2]
- [Benefit 3]

h2. Important Notes
[Any limitations, exclusions, or things the user should be aware of. Include items from "Out of Scope" where relevant to the user.]
- [Note 1]
- [Note 2]

h2. Impact
[Describe the impact on the user experience, business process, or system. Who is affected and how.]
```

---

## Release Notes Writing Guidelines

### DO:
- Write in formal, professional, client-friendly language
- Use plain text only — no Jira wiki markup (no h2., *, #, {note}, etc.)
- Write for end users/clients, not developers
- Use bullet points for lists within sections
- Number steps in "How It Works"
- Derive content from: User Story, Business Context, Feature Description, User Flow, Key Benefits, Acceptance Criteria
- Use Jira wiki markup (h2., *, #, {note}, etc.)

### DON'T:
- Mention internal field names, custom field IDs, or Figma links
- Include acceptance criteria verbatim
- Mention items that are explicitly "Out of Scope" in the story (unless user-relevant)
- Use developer/technical terminology (MudTextField, customfield, OTP inputs, px values, etc.)
- Leave any section blank — write "N/A" if not applicable

---

## Example Output

```
Release Notes – Implement Login Page for SSP

Overview
A new Login page has been introduced in the Self-Service Portal (SSP),
providing users with a secure and streamlined way to authenticate and
access their account.

What's New
- New login page for the Self-Service Portal (SSP)
- Login via MitID for private users
- Login via Customer number with email-based one-time code verification
- Split-screen layout with branded splash imagery

User Access & Permissions
This feature is available to all registered SSP users. Authentication
is required to access the portal and its services.

How It Works
1. Navigate to the SSP login page.
2. Click "Continue with MitID" to authenticate via MitID,
   or click "Continue with Customer number".
3. Enter your Customer number and submit.
4. A one-time verification code will be sent to your registered email address.
5. Enter the 4-digit code to confirm your identity.
6. Upon successful verification, you will be directed to the SSP dashboard.

Key Benefits
- Secure authentication protects customer data and account access
- Multiple login options provide flexibility for different user preferences
- Simple step-by-step flow ensures a smooth login experience
- Instant access to consumption tracking, invoices, and more upon login

Important Notes
- Private user and Business user tab switching is not available in this release
  and is planned for a future update.
- Mobile layout is not supported in this release; the login page is
  optimised for desktop (1440px).

Impact
All SSP users are required to authenticate before accessing the portal.
This change ensures secure, personalised access to utility account data,
invoices, and consumption history.
```

---

## Field Reference

| Field | Field ID | Type |
|-------|----------|------|
| Client Release Notes | customfield_10200 | textarea (plain text) |
| Technical Release Notes | customfield_10217 | textarea (plain text) |
| Include in client release notes | customfield_10211 | select (`Yes` / `No` / `N/A`) |
| Include in technical release notes | customfield_10212 | select (`Yes` / `No` / `N/A`) |

---

## Error Handling

If the update fails:
1. Check that `customfield_10200` is passed as plain text (no wiki markup)
2. Check that `customfield_10211` is passed as a plain string `"Yes"` (not `{"value": "Yes"}`)
3. Verify the issue key exists and belongs to the EGU project
4. Report the specific error to the user with a suggested fix

---

## Notes

- **Project**: Always EGU
- **Issue URL**: `https://jira.eg.dk/browse/EGU-#####`
- **Release notes field**: Plain text only — no Jira wiki markup
- **Include in client release notes**: Always set to `Yes` when generating release notes
- **Confirmation required**: Always show draft and ask for confirmation before updating
- **Custom field format**: Pass as plain strings — NOT as `{"value": "..."}` objects
- **All sections required**: Every section of the format must be present; use "N/A" if not applicable
