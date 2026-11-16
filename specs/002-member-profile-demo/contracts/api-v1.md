# Proposed HTTP API v1

**Status**: Language-neutral Phase 1 contract proposal, not implemented or
accepted. Human security/privacy/tech lead review is required before API,
identity or persistence implementation. [Spec](../spec.md) controls requirements;
[model](../data-model.md) controls proposed invariants. No PII examples are used.

## Common transport and security

- Base path `/api/v1`; JSON request/response documents; UTF-8 Unicode strings,
  UUID resource IDs, ISO calendar dates (`YYYY-MM-DD`), UTC timestamp strings.
  Unknown request properties are rejected, not mass-assigned or silently applied.
  Public breaking changes require a new major version.
- HTTPS with TLS 1.2+; approved exact browser origins only. Proposed bearer access
  tokens use distinct member external-tenant and workforce issuer policies.
  Validate signature, exact trusted issuer, API audience, lifetime and required
  scopes with supported libraries. Do not accept an ID token as an API token.
  Actual audience/scope/entitlement names are configuration-review inputs, not
  invented tenant IDs or claims here.
- `M`: authenticated member issuer, valid API scope. `V`: M plus a server-resolved
  verified identity/profile link. `S`: workforce issuer, valid scope and
  designated staff entitlement. Member-issued role/group-looking claims never
  satisfy S. Route guards and supplied IDs are not authorization.
- No local OTP, password, token-issuance or profile-claim-by-number endpoint.
  Sign-in is the provider's hosted flow. No public audit endpoint or delete API.
- Every resource read/write resolves ownership server-side. On nested routes,
  membership belongs to the resolved profile; request belongs to that membership
  and owning member. Do not authorize only the outer path. Staff also validate
  parent-child consistency. Only entitled staff can use search/list endpoints.
- Sensitive responses, including lists/errors, use `Cache-Control: no-store`.
  No raw bodies, query strings, subject/account references, identifiers or tokens
  in telemetry. All errors include a safe server correlation reference.

## DTO reference

Objects have only the listed properties. Read-only fields cannot be submitted.
Dates, required/nonblank values and demo enums follow the model. Exact bounded
string/request/page sizes must be reviewed before implementation; they are not
production policies or rate limits. Errors identify field names, never values.

<!-- prettier-ignore -->
| DTO | Fields and semantics |
| --- | --- |
| EnrollmentInput | Required `name`, `contactEmail`, `phone`, `dateOfBirth`, `mailingAddress`. |
| EnrollmentView | `enrollmentId`, the five submitted fields, `state` Pending/Verified, nullable `profileId` populated only after verification, `verificationKind` null/SimulatedExisting/SimulatedNew, `createdAt`; `verificationIsSimulated: true` when verified. No identity subject/email or pending profile lookup hints. |
| VerifyEnrollmentInput | Discriminated `mode: "Existing"` plus `profileId`, OR `mode: "New"` with no profileId or replacement profile fields. Creates from submitted fields only in New mode. |
| ProfileView | `profileId`, `memberNumber`, `name`, `contactEmail`, `phone`, `dateOfBirth`, `mailingAddress`. No sign-in email, subject or actor mapping. |
| MemberContactPatch | At least one of `contactEmail`, `phone`, `mailingAddress`; supplied values required/non-null/nonblank. No name/DOB/member number/link fields. |
| StaffProfileInput | All five profile fields for create. |
| StaffProfilePatch | At least one of the five profile fields; supplied values required/non-null/nonblank, valid date/email. |
| ProfileSearchInput | `criterion`: MemberNumber/MembershipNumber/Name/ContactEmail; required `value`; optional opaque `cursor`. Numbers use exact match; proposed name/email matching is case-insensitive literal substring, not SQL wildcard expressions. |
| MembershipInput | Required `type`, `status`, `startDate`; optional nullable `endDate`. Exact demo enums below. |
| MembershipPatch | At least one of `type`, `status`, `startDate`, `endDate`. Null only allowed for endDate; omission leaves unchanged. Whole resulting record must be valid. |
| MembershipView | `membershipId`, `profileId`, `membershipNumber`, `type`, `status`, `startDate`, nullable `endDate`; `classificationIsDemoOnly: true`. |
| ChangeRequestInput | Required nonblank plain-text `desiredChange`. Membership selected by route; no state, actor or approval fields. |
| ReviewDecisionInput | Discriminated `decision: "Approved"` with `approvedChange` (MembershipPatch) and opaque `membershipETag` from the reviewed membership; OR `decision: "Rejected"` with neither field. No arbitrary outcome notes needed. |
| RequestView | `requestId`, `profileId`, `membershipId`, `state` Submitted/Approved/Rejected/Applied, `desiredChange`, `handoff: { simulated: true, outcome: Succeeded/Failed, failureCode: null or safe code }`, `submittedAt`, nullable `decidedAt`, nullable `appliedAt`, nullable `approvedChange`. Outcome visible through state and handoff, not staff identity. |
| Collection of T | `items: T[]`, nullable opaque `nextCursor`; stable server ordering/tie-break; no unbounded dump. Cursor is a navigation value, not authorization or an encoded personal search string. |

Enums are exactly `Demo type A`, `Demo type B`, `Demo active`, `Demo ended`.
No extra production classification/transition rules. An ended membership does
not require an end date; a supplied end date must be on/after its start date.
Never return or accept a member/membership number as proof of ownership.

## Resource and concurrency headers

- GET an individual enrollment/profile/membership/request returns an opaque
  strong `ETag` representing its current version. Collections do not stand in
  for fetching an individual editable resource's ETag.
- All PATCH operations, verification, decision and application require exactly
  the current resource ETag in `If-Match`; wildcard/unconditional writes are
  rejected. Missing conditional header is `428`; stale version is `412`.
- Approval additionally checks `membershipETag` at decision time and captures
  it with the approved payload. Application has **no change payload** and requires
  the membership still match that captured version; otherwise `409` and the
  request remains Approved. Re-review via a new request is the proposed recovery.
- Creates do not require If-Match. Enrollment duplicates conflict rather than
  replacing the current submission. UUIDs/numbers/actors are always generated or
  resolved server-side. No automatic replay guarantee for general POST/PATCH.
- Conditional state transitions, rowversion checks and writes occur within the
  SQL transaction. Two concurrent applies cannot both update membership.
  A replay against Applied returns conflict (or stale precondition when the old
  ETag is used), never applies again or emits a second successful apply audit.
- Successful creates return `201`, `Location` of the created resource, and its
  ETag. Other successful mutations below return `200` with the updated view and
  ETag. A compound apply returns RequestView; fetch MembershipView for its new ETag.

## Member operations

All routes below are prefixed `/api/v1`. Each route/method requires public API
contract tests, including wrong issuer, missing scope, nested ownership, schema,
safe errors and conditional behavior where applicable.

<!-- prettier-ignore -->
| Method / path | Policy | Request -> success | Required behavior |
| --- | --- | --- | --- |
| POST `/me/enrollment` | M | EnrollmentInput -> 201 EnrollmentView | Required five fields; Pending snapshot + audit; no match/lookup/autolink. Duplicate current enrollment 409. |
| GET `/me/enrollment` | M | None -> 200 EnrollmentView | Only submitter's current enrollment, Pending or Verified. 404 if absent. |
| GET `/me/profile` | V | None -> 200 ProfileView | Resolve profile from verified link, never email. |
| PATCH `/me/profile` | V | MemberContactPatch + If-Match -> 200 ProfileView | Only member contact allowlist. Identity/sign-in email/link unchanged. |
| GET `/me/profile/memberships` | V | Optional cursor -> 200 collection of MembershipView | All own memberships including ended history. |
| GET `/me/profile/memberships/{membershipId}` | V | None -> 200 MembershipView | Resolve membership under own profile; no foreign details. |
| POST `/me/profile/memberships/{membershipId}/requests` | V | ChangeRequestInput -> 201 RequestView | Own membership; Submitted request + audit; membership unchanged; simulated handoff outcome explicit. |
| GET `/me/requests` | V | Optional cursor -> 200 collection of RequestView | Only requests for current verified member's own memberships and requester. |
| GET `/me/requests/{requestId}` | V | None -> 200 RequestView | Check requester AND current linked profile/membership; show status/outcome without staff identity. |

Pending members may use enrollment routes only. A 403 for their own profile
request may state that verification is required; it must not confirm whether
any staff-created matching profile exists. A guessed foreign resource returns
the same safe 404 as an absent resource after authentication. There is no member
search, member membership mutation or member request-decision endpoint.

## Staff enrollment/profile operations

<!-- prettier-ignore -->
| Method / path | Policy | Request -> success | Required behavior |
| --- | --- | --- | --- |
| GET `/staff/enrollments` | S | Optional cursor -> 200 collection of EnrollmentView | Pending enrollments only; staff-only queue view. |
| GET `/staff/enrollments/{enrollmentId}` | S | None -> 200 EnrollmentView | Read submitted snapshot and verification state. |
| POST `/staff/enrollments/{enrollmentId}/verification` | S | VerifyEnrollmentInput + If-Match -> 200 EnrollmentView | Pending only; atomically link existing profile OR create profile/number and link; simulated verification metadata + audit. Conflicting links 409. |
| POST `/staff/profiles` | S | StaffProfileInput -> 201 ProfileView | Generate unique member number; no verified identity association inferred. |
| POST `/staff/profile-search` | S | ProfileSearchInput -> 200 collection of ProfileView | Four required search categories; read-only. Body avoids personal search text in URLs; disable body logging. Empty result is empty items. |
| GET `/staff/profiles/{profileId}` | S | None -> 200 ProfileView | Entitled staff may read profile. |
| PATCH `/staff/profiles/{profileId}` | S | StaffProfilePatch + If-Match -> 200 ProfileView | Five fields only; no identity/link/number mutation. |

Proposed staff list/search pagination has a deterministic order with ID tie-break
and reviewed bounded page size. Search-by-membership-number resolves its parent
profile; it is not a member-facing lookup or a verification operation.
Search normalization/index/collation requires test coverage and review; no
uniqueness constraint on name/contact email.

## Staff membership/request operations

<!-- prettier-ignore -->
| Method / path | Policy | Request -> success | Required behavior |
| --- | --- | --- | --- |
| GET `/staff/profiles/{profileId}/memberships` | S | Optional cursor -> 200 collection of MembershipView | Include ended history. |
| POST `/staff/profiles/{profileId}/memberships` | S | MembershipInput -> 201 MembershipView | Generate unique number; persist valid dates/demo enums + audit. |
| GET `/staff/profiles/{profileId}/memberships/{membershipId}` | S | None -> 200 MembershipView | Enforce actual profile/membership relationship. |
| PATCH `/staff/profiles/{profileId}/memberships/{membershipId}` | S | MembershipPatch + If-Match -> 200 MembershipView | Direct staff maintenance, no request state changed; immutable number/owner. |
| GET `/staff/requests` | S | Optional state filter/cursor -> 200 collection of RequestView | Find submitted/approved/rejected/applied requests; no external review claim. |
| GET `/staff/requests/{requestId}` | S | None -> 200 RequestView | Read request and labeled handoff/recorded decision. |
| POST `/staff/requests/{requestId}/decision` | S | ReviewDecisionInput + If-Match -> 200 RequestView | Submitted -> Approved or Rejected only. Approved payload validated against current membership/version and frozen. No membership change. |
| POST `/staff/requests/{requestId}/application` | S | No body + If-Match -> 200 RequestView | Approved only; apply exact approved payload once; membership/request/audit commit together. |

There are **24 public route/method combinations** above; this count is a
contract-coverage inventory, not evidence that endpoints exist.
All successful mutations in all tables require internal successful audit in the
same SQL transaction; compound operations audit the constituent operations/
field names without values. There is no unaudited "seed" or admin write route.

## Simulated handoff and decision semantics

Submitting a request performs only the internal demo simulation, not an external
call. A persisted simulated failure returns 201 RequestView with Submitted and
handoff Failed: creation succeeded, handoff did not. The UI must show both facts.
Record the known simulated handoff failure in a minimal failed-outcome audit
alongside the successful request-creation audit within that transaction.
If persistence or audit fails, return a failure and create no successful request.
Fault-injection belongs to tests/approved demo configuration, not a public
member-controlled outcome property.

A staff decision is an explicit record of simulated review. A prior handoff
failure remains visible; staff recording a later review must not rewrite history
to imply external delivery. Neither handoff success nor approval applies a
membership change. Failed application preserves Approved and the membership;
only successful atomic application returns Applied.

## Proposed error contract

Use a problem-details-style JSON envelope:
`type` (stable nonpersonal problem identifier), `title`, numeric `status`,
safe `code`, `correlationId`, optional `fieldErrors` keyed by DTO field name with
allowlisted textual messages. Do not echo values, raw URLs, resource IDs, search
text, subjects, provider exception text or stack traces. A correlation ID is
generated/validated by the server and is not an authorization credential.

<!-- prettier-ignore -->
| HTTP status | Proposed meaning |
| --- | --- |
| 400 | Malformed JSON, wrong shape/type, unknown or forbidden field, missing/nonblank/date/enum violation; safe field errors. |
| 401 | Missing/invalid/expired token, wrong issuer/audience/signature. No member detail. |
| 403 | Valid caller lacks required scope/policy/staff entitlement or own verification gate. |
| 404 | Resource absent or not accessible to this member; no existence distinction. |
| 409 | Duplicate enrollment, conflicting verified link, illegal/terminal state, or approved membership version no longer current. |
| 412 | Supplied If-Match resource version stale. |
| 428 | Required conditional update header missing. |
| 503 | Required SQL/audit dependency unavailable; no successful unaudited mutation committed. |
| 500 | Unexpected internal failure; safe generic response and correlation only. |

Error/status precedence for overlapping invalid-input, authorization and stale-
state cases must be reviewed before implementation; no precedence may disclose
foreign resource existence. Deny auth/policy before data disclosure. Semantic
field errors are not an excuse to echo member input.

Authenticated failed attempts produce separate restricted failure audit when
available. Unauthenticated requests do not fabricate a member actor; safe
nonpersonal telemetry is not a substitute for an audit claimed to exist.
Do not add production retention, rate limits, retries or service-level
guarantees without requirements/review.

## Minimum contract and integration test matrix

For **every route**: allowed successful schema, absent/invalid token, wrong trust/
scope, nonstaff access to staff routes, invalid/unknown fields where input exists,
safe errors/correlation and no-store handling. For every resource route: foreign
or inconsistent nested IDs and no enumeration. For every mutation: audit success,
rollback on audit failure, authenticated failure audit when available and no
payload leakage. For every conditional write: missing/stale/valid ETag.

Additionally test both verification paths, concurrent unique-link/number races,
each editable field and forbidden field, separate contact/sign-in identity,
all four searches, optional/invalid dates and ended history, request ownership,
failed simulated handoff, Submitted/Rejected apply denial, approval without
mutation, failed/stale/concurrent/repeated application and post-commit readback.
Use actual SQL-provider tests for transactional/concurrency invariants.
None of these tests have been executed against an application.
