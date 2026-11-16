# Proposed Member and Staff UI Contract

Phase 1 interaction proposal for one Angular application, not implemented screens
or approved routing. The [spec](../spec.md) defines acceptance; the
[API contract](api-v1.md) defines proposed server operations. Page names below
describe responsibilities, not promised current browser URLs.

## Shared interaction rules

- Clearly identify the application as a synthetic-data demo. Label verification
  and review handoffs as simulated; label membership types/statuses demo-only.
  Never imply production identity proofing, external delivery or certification.
- Use semantic headings, landmarks, native controls, visible matching labels,
  associated instructions and text errors, logical keyboard order and visible,
  unobscured focus. Preserve OTP paste in the hosted identity experience.
- Associate errors with fields and provide a focusable error summary after
  invalid submission. Do not rely on color, placeholders or visual-only icons.
  Announce meaningful loading, save and outcome changes to assistive technology;
  manage focus predictably on navigation/dialog close, not on every refresh.
- Preserve entered form values after recoverable failure without storing
  sensitive input in URLs, logs or persistent browser storage. Clear sensitive
  view state at logout/account change; never show previous-account cached data.
- Loading is not success; empty results are not failures. Disable duplicate
  submissions while pending without assuming this replaces server concurrency.
  On timeout/uncertain commit, read current state before retrying a mutation.
- Show safe error text/reference from the server. On stale ETag, explain conflict
  and reload/review; never silently overwrite. On expired authentication return
  to the correct hosted trust flow without an insecure local fallback.
- UI guards hide unavailable actions for usability only; the API still enforces
  every authorization/ownership boundary. Do not expose foreign resource details
  in an error or inferred account/profile match.

## Member journeys

<!-- prettier-ignore -->
| Page / state | Allowed actions and visible information | Acceptance and failure behavior |
| --- | --- | --- |
| Sign-in | Choose member hosted email OTP; separate staff sign-in entry | Invalid/expired/reused codes do not create an authenticated application session. Hosted fields accept paste and keyboard use. No custom code service. |
| Enrollment, none | Submit name, contact email, phone, DOB and mailing address | All five required. Identify missing fields with text; no lookup by email/member/membership number or pre-existing-profile confirmation. |
| Enrollment, Pending | Read own submitted snapshot and pending/simulated-verification explanation | No profile edit/navigation to another profile. Foreign/absent identifiers reveal no details; duplicate submit must not replace the enrollment. |
| Enrollment, Verified | Explain simulated verification completed and offer own profile | Do not imply production proofing; do not expose identity subjects or staff audit mappings. |
| Own profile | Read five profile fields, member number and all memberships including ended | Member contact form edits only contact email, phone, mailing address. Name/DOB/membership fields read-only; contact email explicitly separate from sign-in. |
| Own membership detail | Read number, exact demo type/status, start and optional end date | Retain ended history. No direct member edit control; optional end date remains optional. |
| New membership change request | Plain-text desired change for selected own membership | On success show Submitted and simulated handoff outcome, not changed membership. Failed handoff explicitly shows request recorded but handoff failed; no external review implied. |
| My requests / detail | Own desired change, Submitted/Approved/Rejected/Applied and simulated handoff outcome | Approved means reviewed but not applied. Rejected cannot apply. Applied appears only after server confirms atomic update. Show safe application failure without implying a membership change. |

Profile and membership views compose the protected profile and membership reads;
the profile is not a copy of the pending enrollment. Updating contact email does
not alter sign-in, trigger linking, or change account association.

## Staff journeys

All staff views require the designated workforce entitlement, not a member claim
with a similar name. A nonstaff user sees no staff lists or search results.

<!-- prettier-ignore -->
| Page / state | Allowed actions | Acceptance and failure behavior |
| --- | --- | --- |
| Pending enrollments / detail | Read pending submission; choose existing profile or create-from-enrollment; explicitly record simulated verification | Existing target uses staff-only search. Confirm intent; do not auto-link on matching numbers/email. On conflicting link/stale enrollment report safely and grant no access. |
| Profile search | Select member number, membership number, name or contact email criterion; read paged matches | Four supported categories; empty matches state. No query/body logging or member-facing lookup. Results are not evidence of ownership. |
| Profile create/edit | Create or update five profile fields; read generated member number | Number/link/sign-in identity not editable. Missing/invalid field or audit failure is not saved. Reload/review conflicts rather than overwrite. |
| Memberships / detail | Add multiple memberships; edit type, status, dates | Display exact demo-only enums, generated immutable number; require start; optional end cannot precede start. Retain ended memberships without invented production transitions. |
| Request worklist/detail | Find requests and read desired change, simulated handoff and recorded outcome | Original simulated handoff failure remains visible. Finding a request or handoff success does not approve it. |
| Record simulated review | For Submitted, explicitly approve structured membership field changes against reviewed membership version, or reject | Show exact proposed field changes for staff review. Record decision separately; no membership update. No claimed real external reviewer or mandatory second-staff separation. |
| Apply approved change | For Approved, explicit separate apply action with frozen payload | No editable replacement payload. Applied only on server success; failed audit/update/stale membership leaves Approved and membership unchanged. Already Applied/Rejected cannot be applied again. |

Independent staff membership maintenance is separately labeled; it cannot mark a
request Applied or imply an unapproved/rejected request was processed. If it
changes a membership already reviewed by a request, request application reports
conflict rather than overwriting. Proposed stale-approval recovery is a newly
reviewed request, not silent payload alteration.

## Accessibility and validation evidence

Validate all eight stories, not only the member contact form. Check keyboard,
focus visibility/order/restoration, text labels/instructions/errors and
meaningful status announcements with actual assistive technology. Include hosted
OTP paste and failure states, staff search empty state, link conflict, stale edit,
audit failure, simulated handoff failure, approval without application, and failed
application. Automated scans alone do not prove these behaviors or broader WCAG
conformance. No browser/assistive test has been run for this nonexistent UI.

Do not install browser instrumentation or send direct browser telemetry to
Application Insights: its JavaScript SDK cannot meet the required Entra ingestion
configuration. Review server telemetry separately under [plan](../plan.md).
