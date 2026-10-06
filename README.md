# DGJ CRM — extended mockup workspace

Open `index.html` in Chrome or Edge. The app uses local JavaScript and supplied assets; there is no build step, external CDN or package installation. `DGJ-CRM.html` is the same workspace packaged as one self-contained file.

## What is included

- Contacts: four live KPI cards; top-company bars; tag distribution; location map and distribution; company, position, tag, owner, status and date filters; sortable tables; pagination; columns; saved views; list/month/week/day calendar views.
- Contact profiles: overview, timeline, company history, contacts at the same company and on the same opportunity, linked opportunities, meetings, tasks, reports, complaints, recorded communication, notes, files and details.
- Opportunities: date periods, pipeline, duration timeline, stage history, average stage durations, service distribution, opportunities over time, pipeline value, filters, sortable tables and full profiles. Contacts can have specific roles; additional companies can be linked inside an opportunity.
- Opportunity creation: four-step wizard, a name/company similarity warning, review of the existing opportunity, an optional coordination note and an explicit Continue as New action. Similarity uses word overlap and company equality; it is not an AI assessment. Coordination notes are saved locally and can be copied; no message is sent.
- Meetings: month/week/day calendar, date navigation, upcoming meetings, report state, scheduling conflicts, meeting detail drawer, attendees, links, actions, calendar downloads and follow-up tasks.
- Reports: Meeting, Issue, Complaint, Client Feedback, Follow-up and Other. Each type has its own form, with linked records, review, drafts, submission, editable detail drawer and live analytics. The manager can export a printable HTML report or a filtered CSV.
- Meeting reports: submitting saves the report, completes its linked meeting and creates the specified follow-up tasks in one transaction. Drafts do not complete meetings or create tasks. Editing existing report actions updates their tasks by ID rather than duplicating them.
- Complaint reports: submitting creates or updates the linked complaint record. New complaints and issues start Open. Resolving a complaint report updates its complaint record.
- Tasks: current date-based overdue/today states, priority, assignee, linked records, completion, My/All access behavior, filters, status chart and deadline list.
- Notifications: ended-meeting reports, overdue tasks, complaints, possible contact duplicates and today's meetings. Remind Me Later snoozes a report reminder for two hours. These indicators refresh while the page is open; they do not send email, push or background notifications.
- Import: CSV and vCard parsing, duplicate/invalid-email preview and selective import. Business cards can be uploaded as an image for visual reference; the user enters/reviews the card details. Automatic card recognition/OCR is not connected.
- Duplicate review: compare old/new fields and select what to update. Updating a current company or position retains the prior role and previous record snapshots.
- Notes and attachments: stored in the relevant profile; attachment downloads are functional. Limit: 1.5 MB per file and 3 MB in total, because this standalone workspace uses browser storage.
- Existing manager-only delete/export/trash controls, double-confirmation deletion, restore, permanent deletion and backup/restore are retained. Soft deletion also offers Undo. Permanent deletion removes that record's direct notes and files; dependent records are not automatically reassigned or relinked.

## Keep the existing data

The app continues using the existing `dgj-crm-v1` storage key and stable record IDs. V1–V3 records are accepted and supplementary fields are added without replacing existing records. Fresh demo records have example profile tags and dates to demonstrate the new charts; these do not overwrite saved records.

Browser storage belongs to the current browser/origin. Opening another filename or hosting it elsewhere may use a separate storage area. Before moving an existing workspace, select the General Manager demo profile and download a backup. Restore that backup into the new location.

Charts count the records the current view can access. Filters and manager All/My scopes also affect the counts, tables and exports. Untagged locations, missing original dates and missing recorded stages are shown honestly. Tag percentages count tag assignments because a contact can have several tags. Opportunity duration charts exclude records whose start dates have not been recorded.

All times and calendar downloads use Asia/Riyadh. There is no automatic 90-day purge; trash remains recoverable until the manager explicitly deletes it permanently.

## Files

- `index.html`: shared navigation and header.
- `style.css`: original visual design.
- `workspace.css`: consistent additional layouts and responsive styling.
- `app.js`: original records and core workflow layer.
- `workspace.js`: filters, analytics, tables, calendars, notifications and creation/report flows.
- `profiles.js`: full profiles, history, relationships, notes and attachments.
- `interactions.js`: interaction routing, import, duplicate review, access inheritance and export.
- `assets/`: original supplied palm, map and banner.
- `LISTS-MAPPING.md`: proposed fields for the supplied Microsoft Lists.
- `PRODUCTION-INTEGRATION.md`: identity, authorization, storage and email integration requirements.
- `VALIDATION.md`: the actual verification performed for this version.

## Integration status

Storage is in this browser, as in the supplied source. Microsoft Lists, Entra ID authentication, Teams booking and Outlook sending are not connected. A saved meeting is a CRM record; an ICS download can be opened in a calendar app. Joining uses the HTTPS meeting link supplied by the user. Email composers create drafts or open the user's email app and never claim to send the message.

Demo profile switching remains a UI demonstration, not authentication or a security boundary. The existing production integration contract still applies before using sensitive company data in a shared deployment.
