# Required production integrations — not implemented in this demo

The frontend currently runs entirely locally. It makes no authentication,
database or mail API requests. Hiding controls and checking JavaScript roles is
not server authorization. This document describes the contract to implement;
it is not a claim that these services are deployed.

## Identity and roles

Use the company's Microsoft Entra workforce tenant for employees, with a
single-tenant app registration and a registered production redirect URI.
Use an approved Microsoft authentication library with authorization code + PKCE
for browser sign-in. Enforce the company's MFA/Conditional Access policy.

The company administrator assigns the General Manager app role/group. Users
must not select their own role. The backend validates tokens intended for its
API, including signature, tenant, issuer, audience and expiry, then maps trusted
role claims. Use the tenant ID + object ID for ownership, not a name, email
string, form field or browser-provided owner/role.

No client secret, application mailbox credential or database admin credential
belongs in browser code. Store backend secrets in the hosting platform's secret
store and restrict access. Serve via HTTPS with appropriate content-security
policy and keep dependencies updated.

## Storage and authorization

The company's earlier choice was Microsoft Lists. SharePoint supports
list/item-level permissions. Confirm list design, expected record volume,
permissions, app registration and custom-backend hosting with IT before
connecting this frontend. Do not assume every needed custom integration is
covered by existing Power Platform standard-connector licensing.

The backend or storage access policies must enforce these rules for every
request, not merely filter records that have already been sent to the browser:

| Operation | Member | General Manager |
| --- | --- | --- |
| Read shared active records | Yes | Yes |
| Read private records | No | Own private records only |
| Create shared records | Yes, owned by caller | Yes, owned by caller |
| Create private contacts/opportunities | No | Yes, owned by caller |
| Edit shared records | Yes, per the demo's current behavior | Yes |
| Change visibility | No | Own contacts/opportunities only |
| Soft-delete / restore / purge | No | Authorized records only |
| Export / backup / restore backup | No | Authorized records only |
| View Trash | No | Authorized deleted records only |
| View activity | Own activity, authorized records | All/My, authorized records |

Apply rules to search, pagination, charts, counts, activity, change history,
recipient lookup, exports and emails as well as record-detail endpoints.
A shared/private flag in a List is not sufficient if members retain broad direct
read permissions to the List. Design item permissions or a backend-only data
access path so private records cannot be read directly. Prevent members from
bypassing the CRM deletion restriction through underlying List permissions.
Ensure deleted items are inaccessible to members even through direct storage
access and search. Sharing a manager-private record must update storage access
controls before exposing it in the UI.

Separate Trash is one option; a soft-delete field alone does not secure deleted
data. Decide retention, recovery and permanent-deletion policy with IT. Preserve
server audit events, including actor, action, time and record identifiers, rather
than relying on editable local logs. Avoid logging confidential message content
or tokens. Protect backups and minimize data returned to browsers.

Manager-only export restricts the export operation; it cannot prevent a person
from copying information they are already authorized to read.

## Example backend contract

- GET /api/me — trusted user ID, display name and assigned role.
- GET /api/records/:type?scope=all|my&query=... — only authorized active rows.
- POST /api/records/:type — derive owner from authenticated identity.
- PATCH /api/records/:type/:id — validate permissions and a revision/ETag.
- POST /api/contacts/duplicates — authorized matches only, with match reasons.
- POST /api/contacts/:id/merge — explicit user decision, transactional update,
  preserve owner/visibility and maintain history.
- POST /api/records/:type/:id/trash — manager, authorized record, soft-delete.
- GET /api/trash?scope=all|my — manager, authorized deleted records only.
- POST /api/trash/:type/:id/restore — manager, reapply prior access policy.
- DELETE /api/trash/:type/:id — manager, purge according to retention policy.
- GET /api/export/:type?scope=... — manager, authorized filtered rows only.
- GET /api/opportunities/:id/recipients — authorize opportunity and contacts.
- PUT /api/opportunities/:id/email-drafts/:draftId — caller-owned draft.
- POST /api/opportunities/:id/emails/send — authenticate sender, authorize
  opportunity, validate recipients, rate-limit and record a server audit event.

These are proposed routes. There is no deployed API behind the delivered code.
Handle concurrent edits and duplicate creation on the server; client checks
alone cannot prevent races between colleagues.

## Outlook email

Microsoft Graph supports delegated Mail.Send to send as the signed-in employee.
A company shared-mailbox sender is a separate setup requiring the appropriate
mailbox and Graph permissions. Have IT approve the sender model and consent.
Request only needed permissions; do not assume access to every company mailbox.

The composer can remain unchanged, but replace the local mailto action with an
authenticated backend flow after integration. Use an idempotency key and safe
retry handling to avoid duplicate sends. Microsoft Graph's HTTP 202 means the
request was accepted, not proof of delivery. Show accepted/failed/unknown states
accurately and track delivery separately if needed. Never record 'Sent' merely
because a draft was saved or an email client opened.

## Microsoft documentation

- https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-single-page-app-sign-in
- https://learn.microsoft.com/en-us/sharepoint/dev/scenario-guidance/security
- https://learn.microsoft.com/en-us/graph/api/user-sendmail?view=graph-rest-1.0
