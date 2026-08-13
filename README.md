# TimeOffWizard

TimeOffWizard is a UiPath automation project that processes employee time-off requests received by email, evaluates available vacation balance, and prepares an approval or rejection response.

## Repository Description (for GitHub)

**Suggested short description:**  
UiPath RPA workflow that automates PTO request handling using Outlook 365 and Google Sheets balance tracking.

## What the project does

The automation:
- Reads incoming emails from an Outlook 365 inbox.
- Extracts the sender email and requested number of leave days.
- Checks and updates employee leave balances in a Google Sheet.
- Decides approval/rejection based on available days.
- Drafts an email response to the requester.
- Marks processed emails as read.

## Technology Stack

- **Platform:** UiPath Studio (Windows project profile)
- **Workflow language:** XAML workflows (Visual Basic expressions)
- **RPA packages:**
  - UiPath.System.Activities `25.8.1`
  - UiPath.Excel.Activities `3.3.1`
  - UiPath.GSuite.Activities `3.5.10`
  - UiPath.MicrosoftOffice365.Activities `3.5.10`
- **External systems:**
  - Microsoft 365 Mail (request intake + response draft)
  - Google Sheets (leave balance storage)

## Architecture Overview

The solution is split into two workflows:

1. **`Main.xaml` (orchestration layer)**
   - Polls unread emails from Inbox.
   - Parses request details from each email.
   - Invokes `GoogleSheetCalculation.xaml` for business decisioning.
   - Drafts a rejection/approval email.
   - Marks message as read.

2. **`GoogleSheetCalculation.xaml` (business rules + data layer)**
   - Reads current employee balance table from Google Sheets.
   - Creates table headers if sheet is empty.
   - Matches requester email against existing records.
   - Applies leave-balance rules and updates sheet values.
   - Returns remaining days to caller workflow.

## Core Business Rules

- Existing employee:
  - If remaining days are lower than requested days -> request is rejected.
  - Otherwise, used and remaining days are updated and request is approved.
- New employee:
  - Starts from a default annual allocation of **20 days**.
  - If request exceeds remaining days -> request is rejected.
  - Otherwise, a new row is appended in the sheet and request is approved.

## Data Model (Google Sheet)

`Sheet1` columns:
- `Email`
- `Total_Zile` (total days)
- `Zile_Folosite` (used days)
- `Zile_Ramase` (remaining days)

## Project Structure

- `Main.xaml` - entrypoint workflow
- `GoogleSheetCalculation.xaml` - leave calculation/update workflow
- `project.json` - UiPath project metadata, dependencies, runtime settings
- `project.uiproj` - UiPath project descriptor

## Setup & Run

1. Open the project in **UiPath Studio**.
2. Restore dependencies from `project.json`.
3. Configure Microsoft 365 and Google Workspace connections in UiPath Integration Service/Connection Service.
4. Verify:
   - Inbox folder selection for request intake.
   - Target Google Sheet and worksheet (`Sheet1`).
5. Run `Main.xaml`.

## Operational Notes

- Responses are currently saved as **draft emails** (`SaveAsDraft=True` in workflow).
- Request-day parsing is based on the first numeric value found in email body (regex).
- The automation is currently configured as an unattended process profile with user interaction required by runtime options.

## Why this project is relevant

This repository demonstrates:
- End-to-end RPA process design
- Integration across Microsoft 365 and Google Workspace
- Practical business-rule automation
- Data-driven decision workflows and state updates 
