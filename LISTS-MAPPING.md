# Mapping the supplied Microsoft Lists to the CRM profiles

The screenshot shows seven Lists under Sandbox KSA. It shows list titles only;
it does not establish their columns, internal field names, IDs or permissions.
The Employment History title is truncated in the screenshot and must be confirmed.

| Supplied List | Proposed purpose |
| --- | --- |
| CRM - Contacts | Contact identity, company, role, methods of contact, tags and visibility |
| CRM - Opportunities | Opportunity/client identity, stage, value, scope and linked contacts |
| CRM - Meetings | Meetings, attendees, linked opportunity, outcome and next actions |
| CRM - Tasks | Tasks, owner, due date, status and linked contact/opportunity |
| CRM - Reports | Report records, category/content/status and linked contact/opportunity |
| CRM - ActivityLog | Recorded calls/emails/messages/notes and system change events |
| CRM - Employment Hi… | Contact employment history; confirm full List title |

Suggested relationship fields (subject to the actual schema):

- Contacts: a stable record ID and Microsoft List item ID, plus trusted owner ID,
  visibility, created/updated metadata, profile and contact-method fields.
- Opportunities: linked Contact IDs (multi-valued relationship).
- Meetings/Tasks/Reports: linked Contact IDs and linked Opportunity ID.
- ActivityLog: target record type/ID, linked Contact IDs, linked Opportunity ID,
  event type, channel, subject/details, occurred time, actor and outcome.
- Employment History: linked Contact ID, company, role, department, start/end dates.

Do not create duplicate columns before inspecting the existing lists. SharePoint
lookup fields can implement these relationships; requests must use actual
internal field names, and Microsoft List item IDs must be mapped to the existing
local stable IDs. Do not link records by display name alone.

Profiles are aggregated views, so a new List per profile is unnecessary.
Private parent access must cover every dependent record in all data-access paths.
A relationship column alone does not enforce permissions on related items.
Audit timestamps/actors must come from authenticated server operations in the
production app. System audit events should not be editable by ordinary members.

Current Contacts and Opportunities contain UI demo records only. There is no
active connection to these Lists. The next integration inputs are the SharePoint
site/List URLs, actual columns/internal names and IT-approved Entra app settings.

## Decisions still needed before integration

- Confirm the Complaints storage model: the screenshot contains no Complaints
  List. Decide on a separate List or an explicitly approved record type in an
  existing List before connecting the implemented Complaints page.
- Decide where server-backed email drafts and email status will live. ActivityLog
  can be a candidate if its approved schema and permissions accommodate this.
- Review attachments/document storage separately. The demo has no document
  upload/download implementation or SharePoint document-library integration.
- Decide how Trash is secured and how dependent records are retained/purged.
- Confirm production hosting and least-privilege API/storage permissions with IT.

## V4 workspace fields (proposed, not connected)

The additional forms and profiles continue using stable local record IDs.
No extra list per profile or report type is required. Verify actual SharePoint
internal column names before mapping any field below.

- Contacts: JobTitle, Department, Tags, Location, PreferredChannel, LinkedIn and
  optional DateAdded, in addition to the existing identity/contact columns.
- Opportunities: StartDate, TargetDate, Probability, OpportunityType, Source,
  LastStageChangedAt, ClosedDate, StageHistory, ContactRoles and LinkedCompanies.
  The history/role/company collections need an approved JSON or related-list model.
- Meetings: Date, StartTime, EndTime, URL, MeetingType, ReportRequired, ReportID,
  ContactIDs, OpportunityID and ExternalAttendees. Store times consistently in UTC
  and show Asia/Riyadh when using company services.
- Reports: ReportType, Summary, Details, KeyTakeaways, Decisions, NextSteps,
  Severity, Priority, Sentiment, Category, Source, Resolution, FollowUpDate,
  MeetingID, ContactIDs, OpportunityID, Status, SubmittedAt and Actions.
- Tasks: AssigneeID, Priority, DueDate, CompletedAt, ReportID, MeetingID,
  ContactIDs and OpportunityID. Preserve generated action/task IDs to avoid
  duplicate tasks when a submitted report is edited.
- Complaints: the implemented standalone report can create a linked local
  Complaint record. The company storage location for that record is still to
  be approved, as the supplied screenshot did not contain a Complaints List.
- Notes: a profile note is a separate local record. Map to an approved ActivityLog
  entry or related record using explicit parent type/ID and author information.
- Attachments: local files are now implemented for the standalone workspace.
  For company use, replace their local base64 storage with an approved SharePoint
  document library, with the same parent access rules and file validation.
- Saved views and snooze preferences are per user; they do not affect record
  permissions. Keep them local or map them to an approved per-user settings store.

Submitting a meeting report currently updates records together in one local
transaction. A production flow must provide retry/idempotency behavior for the
meeting update, report save and action-task creation, and keep audit actors and
timestamps tied to the signed-in identity.
