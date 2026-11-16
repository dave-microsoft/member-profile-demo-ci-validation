# Tasks: Member Profile Demo

**Input**: [spec](spec.md), [plan](plan.md), [research](research.md),
[data model](data-model.md), [API contract](contracts/api-v1.md),
[UI flows](contracts/ui-flows.md), and [validation guide](quickstart.md).

**GitHub Milestone**: [Member Profile Demo milestone 3](https://github.com/dmd0822/member-profile-demo/milestone/3).

**Status**: Proposed implementation backlog, generated after planning approval
only. All tasks remain unchecked. Schema, bounds, HTTP and dependency choices
remain Proposed until T001 records the required human decisions. No task grants
implementation, resource provisioning or deployment authority by itself.

**GitHub tracking**: Exactly 24 tasks, T001–T024, each linked to its actual open
issue using the supplied map. Reuse these task IDs, milestone and issues; keep
acceptance, dependencies and completion evidence synchronized without creating
duplicates. Issue milestone assignments and dependency links are synchronized;
issue creation does not complete any task. The external
foundation prerequisite is [003 T012](../003-initial-app-projects/tasks.md),
[issue 30](https://github.com/dmd0822/member-profile-demo/issues/30).
Its completion is required where listed, not assumed from the existence of files.

## Format and execution rules

- Each task has one checkbox, fixed ID, story label when applicable, concrete
  proposed paths, a verbatim model constraint, and exactly one actual issue link.
  Additional acceptance bullets form part of that same task, not extra tasks.
- `[P]` marks only disjoint test files after all listed prerequisites. Test
  authors must not concurrently edit shared harness/configuration/fixtures.
  Shared implementation tasks are serial; narrative phase order and numeric IDs
  are not the execution schedule. The dependency table below is authoritative.
- Test tasks write acceptance-derived tests and observe behavioral assertion
  failures before the corresponding implementation. A missing feed, compiler,
  environment or runner is a blocker, not valid fail-first evidence.
- Tests may use isolated synthetic fixtures and isolated selectors while other
  story routes are unbuilt. Do not require upstream feature implementations
  merely to create test records. No runtime authorization bypass is permitted.
- Keep all tests outside `src/`. Use 003's reviewed Angular/CLI 22,
  Node.js >=24.15.0 <25, TypeScript 6.0.x, RxJS 7.8.x and .NET 11 baseline.
  Approve exact package pins/feeds/license/vulnerability results before retrieval.
- Every story mutation uses T011's atomic audit and safe-error enforcement;
  every story UI uses T013's semantic form/focus/error foundation. Execute US4
  and US5 prerequisites early even though their story phases appear later.
- Future implementation paths below are proposed targets, not existing files
  or authorization to create a second runtime. Reuse the SAME 003 backend project.
- In path lists, abbreviated `Program.cs`, `Api/`, `Models/` and `Services/`
  paths are relative to `src/backend/`; abbreviated frontend `components/`,
  `services/` and `pages/` paths are relative to `src/frontend/src/app/`.
  Unqualified planning artifact filenames refer to `specs/002-member-profile-demo/`.
- Exact schema string lengths, DTO/request/page bounds, number representation,
  claim/scope names and overlapping HTTP error precedence are T001 decisions.
  Do not convert an undecided technical choice into invented production policy.

## Phase 1: Setup and human review

**Goal**: Record human decisions without presuming implementation approval or a
completed foundation. Independent evidence is the named reviewer/decision record.

- [ ] T001 Record human design/security/privacy/dependency review and exact bounds/schema decisions in `specs/002-member-profile-demo/plan.md`, `research.md`, `data-model.md` and `contracts/api-v1.md`; preserve the model constraint "Exact storage lengths and transport bounds require contract/schema review before implementation; do not silently truncate or expose values in errors." [Issue](https://github.com/dmd0822/member-profile-demo/issues/37)
  - Dependencies: none for review. Review is allowed before 003 T012; implementation
    still requires that foundation wherever the dependency table specifies it.
  - Record tech lead, security champion and privacy owner decisions on separate
    member/workforce issuer/audience/scope policies, hosted primary email OTP,
    staff entitlement, immutable identity/link invariants and restricted actors.
    Record actual names/statuses, not generated approval.
  - Review exact schema lengths, nullable/required columns and database checks,
    generated-number representation/collision handling, normalization/collation,
    transport/page bounds, DTOs, ETag/If-Match and HTTP precedence, frozen review
    payload and stale-approval recovery. Keep production retention, minors,
    status policy and rate limits unselected unless separately specified.
  - Approve same-project ASP.NET Core conversion, EF Core or reviewed parameterized
    SQL alternative, exact compatible SDK/dependencies, internal feed access,
    public/private package classification, license/vulnerability results, external
    test discovery and browser/assistive test approach. Feed failures block retrieval.
  - Record server-only Entra telemetry review and reconcile older Proposed
    hosting/queue/browser-telemetry language; hosting remains unselected until
    explicitly reviewed. No queue, worker, new runtime or infrastructure is added.

## Phase 2: Foundational tests and API baseline

**Goal**: Establish reviewed common primitives in the existing application set.
No domain write endpoint may ship before T011, and no story UI before T013.

- [ ] T002 Write fail-first common auth/ownership/transaction/correlation foundation tests in `tests/unit/backend/FoundationTests.cs`, `tests/integration/backend/FoundationTransactionTests.cs`, `tests/contract/backend/Support/ApiFixture.cs`, `tests/contract/backend/MemberProfile.Backend.ContractTests.csproj` and `tests/integration/backend/MemberProfile.Backend.IntegrationTests.csproj`; assert "Updates compare the supplied version in the SQL write, not merely in a prior read." [Issue](https://github.com/dmd0822/member-profile-demo/issues/36)
  - Dependencies: T001 and 003 T012 / issue 30.
  - Establish the approved compiling contract harness and synthetic actor/record
    factories before parallel tests; discover nonzero tests and record expected
    behavioral failures, not missing SDK/package errors.
  - Test signature/issuer/audience/lifetime/scope validation, member versus
    workforce entitlement separation, server-derived ownership and nested IDs,
    no email/number linking, safe 401/403/404/correlation/no-store behavior and
    conditional-write transaction seams. No real account data, codes or tokens
    enter fixtures or reports; controlled signed test credentials stay isolated.
  - Quote and test identity uniqueness: "Restricted strings; unique pair" and
    "Issuer is canonical configured exact issuer, not arbitrary request input."
    Authorization doubles are test-only and cannot weaken deployed validation.

- [ ] T003 Adapt the SAME 003 backend to API and implement the reviewed SQL/identity/transaction/server telemetry baseline in `src/backend/MemberProfile.Backend.csproj`, `Program.cs`, `Api/IdentityPolicies.cs`, `Api/ProblemResponses.cs`, `Models/MemberDbContext.cs`, `Models/MemberIdentity.cs`, `Models/AuditActor.cs`, `Models/AuditEvent.cs` and `Services/MemberDataSession.cs`; preserve "SQL primary keys, foreign keys and unique constraints are authoritative." [Issue](https://github.com/dmd0822/member-profile-demo/issues/38)
  - Dependency: T002. Convert the existing project, not a second API runtime.
    Wire the common harness without adding story mutation endpoints before T011.
  - Implement reviewed identity/audit relational baseline and future migration
    ownership: identity/actor/event IDs "UUID PK"; identity issuer/subject
    "Restricted strings; unique pair"; actor mapping "unique within trust";
    identity audit actor "UUID FK, unique"; trust kind
    "Member / Workforce / Service", with "not an entitlement."
    Creation/audit times are server UTC. Tokens/codes/passwords are never stored.
  - Set up rowversion/ETag, SQL transaction and managed-identity connection seams,
    safe correlation and allowlisted server telemetry with Entra ingestion/local
    auth disabled. API access-token validation is issuer-specific; workforce-only
    staff entitlement cannot come from member claims. Restrict CORS/redirects
    to reviewed origins and keep browser tokens out of unsafe persistent storage.
  - Respect "Create a missing actor mapping within the audited transaction; if required mapping/audit persistence fails, fail the business mutation."
    T011 supplies the enforced audit execution wrapper before story writes.
    No custom OTP, queue, worker, browser telemetry proxy, infrastructure or
    provisioning. Use separately approved resources only when available.

## Phase 3: US1 — Start Enrollment Safely (P1)

**Goal**: Hosted sign-in and an owned pending submission, without profile claims.
**Independent test**: Using isolated synthetic identities, submit all required
fields; read only the submitting account's enrollment; reject invalid/replayed
OTP, missing fields, foreign data access and email/number claim attempts.

### Tests before implementation

- [ ] T004 [P] [US1] Write enrollment/hosted OTP acceptance and 2-route contract tests in `tests/unit/frontend/enrollment.spec.ts`, `tests/integration/backend/EnrollmentTests.cs` and `tests/contract/backend/EnrollmentContractTests.cs`; assert "No pending enrollment edit or resubmission workflow is required by the spec; duplicate creation returns a safe conflict instead of replacing it." [Issue](https://github.com/dmd0822/member-profile-demo/issues/33)
  - Dependency: T003. Cover routes R01–R02 below; valid/invalid/expired/reused
    hosted OTP, all five missing-field cases, own Pending snapshot/no profile
    edit, no existence disclosure/auto-link by email or numbers, auth/no-store/
    safe errors, duplicate creation and audit rollback. Use isolated provider
    simulation for deterministic tests; actual hosted-flow review remains required.
  - Verbatim field constraints: name/contact email/phone/address
    "Required Unicode strings"; DOB "Required date"; identity
    "FK MemberIdentity, unique"; state "`Pending` or `Verified`".
    Test syntactically valid contact email without treating it as identity.

### Implementation

- [ ] T005 [US1] Implement member hosted sign-in/enrollment API/UI in `src/backend/Api/EnrollmentEndpoints.cs`, `Models/Enrollment.cs`, `Services/EnrollmentService.cs`, `src/frontend/src/app/services/member-auth.service.ts` and `pages/enrollment/`; preserve "Only the submitter or entitled staff can read it." [Issue](https://github.com/dmd0822/member-profile-demo/issues/34)
  - Dependencies: T004, T011, T013. Make R01–R02 tests pass through the common
    policies, audit wrapper and semantic UI foundation. Required five-field
    snapshot, Pending-only own details, duplicate conflict and no matching-profile
    disclosure. MemberIdentity association comes from trusted issuer/subject.
  - Implement model constraints "EnrollmentId | UUID PK", name/contact email/
    phone/address "Required Unicode strings", DOB "Required date", identity
    "FK MemberIdentity, unique"; state "`Pending` or `Verified`".
    Verification fields remain null until staff verification; capture server
    CreatedAt and rowversion. No edit/resubmit API or local OTP service.

## Phase 4: US2 — Verify an Enrollment (P1)

**Goal**: Entitled staff record simulated verification to an existing or new
profile. **Independent test**: Fixture pending enrollments/profiles; exercise
both paths, conflicting concurrent links and nonstaff denial without access grant.

### Tests before implementation

- [ ] T006 [P] [US2] Write staff verification tests and 3-route contracts, including unique-link races, in `tests/unit/frontend/staff-verification.spec.ts`, `tests/integration/backend/VerificationTests.cs` and `tests/contract/backend/VerificationContractTests.cs`; assert "A competing identity cannot claim an already-linked profile." [Issue](https://github.com/dmd0822/member-profile-demo/issues/35)
  - Dependency: T003. R03–R05; use fixtures, not T005 as a test prerequisite.
    Verify existing/new, Pending-only transition, wrong issuer/entitlement,
    missing/stale If-Match, profile/account conflict races, full rollback of
    failed new-profile/link/audit and no matching-number/email authorization.
  - Quote both invariants: "An identity cannot gain multiple profiles by racing verification."
    "A database uniqueness conflict rolls back the entire verification operation, including new-profile creation."
    Profile link identity is "PK/FK MemberIdentity", profile is
    "FK MemberProfile, unique", enrollment is "FK Enrollment, unique".

### Implementation

- [ ] T007 [US2] Implement staff enrollment verification API/UI for existing/new paths in `src/backend/Api/VerificationEndpoints.cs`, `Models/MemberProfile.cs`, `Models/VerifiedProfileLink.cs`, `Services/VerificationService.cs` and `src/frontend/src/app/pages/staff-verification/`; preserve "Verification creates the link and changes the enrollment in one transaction." [Issue](https://github.com/dmd0822/member-profile-demo/issues/39)
  - Dependencies: T005, T006, T011, T013. Implement R03–R05; existing profile
    fields are not overwritten from enrollment. New profile copies only the
    five submitted fields and receives a unique generated member number.
  - Enforce profile ID "UUID PK"; member number
    "Application-generated immutable nonblank string, unique"; profile fields
    "Required Unicode strings"/"Required date"; created time/rowversion.
    Link identity "PK/FK MemberIdentity", profile "FK MemberProfile, unique",
    enrollment "FK Enrollment, unique"; actor/time restricted and server-set.
  - Verification profile/actor/time fields are
    "required exactly when Verified"; verification kind is
    "Null when Pending; `SimulatedExisting` or `SimulatedNew` when Verified".
    Show simulated verification explicitly; database races cannot grant access.

## Phase 5: US3 — View and Update My Profile (P1)

**Goal**: Verified own profile/history and contact-only self-service.
**Independent test**: Fixture a verified link and multiple/ended memberships;
test each allowed contact update, forbidden fields, unchanged sign-in association
and cross-member profile/membership denial. Final integration includes T017.

### Tests before implementation

- [ ] T008 [P] [US3] Write member profile/contact/history tests and 4-route contracts in `tests/unit/frontend/member-profile.spec.ts`, `tests/integration/backend/MemberProfileTests.cs` and `tests/contract/backend/MemberProfileContractTests.cs`; assert "Member self-service updates only contact email, phone and mailing address." [Issue](https://github.com/dmd0822/member-profile-demo/issues/42)
  - Dependency: T003. R06–R09; synthetic memberships avoid a T017 dependency
    for test authoring. Cover own fields/history, each allowed contact field,
    name/DOB/membership/number/link overposting, null/blank/type errors,
    missing/stale ETag, wrong member/nested membership IDs and audit failures.
  - Preserve "Neither path changes identity, sign-in email, member number or verified links."
    Test required profile strings/date and history including null end dates.

### Implementation

- [ ] T009 [US3] Implement member profile/contact/membership-read UI/API in `src/backend/Api/MemberProfileEndpoints.cs`, `Services/MemberProfileService.cs`, `src/frontend/src/app/services/member-profile.service.ts` and `pages/member-profile/`; preserve "Ended memberships are retained and returned with their original identifier and dates; they are not deleted or hidden by a default active-only filter." [Issue](https://github.com/dmd0822/member-profile-demo/issues/44)
  - Dependencies: T007, T008, T011, T013, T017. Implement R06–R09; only contact
    email/phone/address are mutable. Use narrow DTOs, current verified link and
    nested ownership on every read/write; no browser-only authorization.
  - Reuse profile constraints "Required Unicode strings", "Required date" and
    "Application-generated immutable nonblank string, unique"; preserve
    rowversion and identity/link/sign-in separation. Read model membership enums/
    dates without introducing member edit controls or filtering ended history.
    T008 may run against fixtures until T017 is integrated; completion requires
    actual staff-maintained memberships, not only fixture success.

## Phase 6: US4 — Protect and Account for Changes (P1)

**Goal**: Atomic audit, privacy-safe telemetry and failures across every mutation.
**Independent test**: Fault-inject transactions/audit/collector paths using
synthetic operation drivers; later rerun the same assertions across every actual
write route. T011 is an EARLY prerequisite, not end-of-project hardening.

### Tests before implementation

- [ ] T010 [P] [US4] Write privacy/audit-failure/redaction tests across mutations and rollback cases in `tests/integration/backend/AuditAtomicityTests.cs`, `tests/contract/backend/PrivacyErrorContractTests.cs` and `tests/unit/backend/TelemetryRedactionTests.cs`; assert "Never return a successful unaudited mutation." [Issue](https://github.com/dmd0822/member-profile-demo/issues/43)
  - Dependency: T003. Test operation drivers/fixtures before story routes exist;
    bind the same cases to actual mutation routes in T022/T024.
  - Audit insert/commit failure must roll back enrollment, verification/new
    profile/link, profile update, membership creation/edit, decision and apply.
    Failed apply preserves Approved/membership. Lost commit acknowledgment
    requires state readback, not blind retry. Distinguish succeeded creation with
    failed simulated handoff from failed persistence.
  - Assert "Do not store descriptions, search terms, raw identifiers from rejected paths, request/response payloads, stack traces, tokens, codes or profile values in audit."
    Logs/traces additionally exclude identity/actor/account metadata, raw paths/
    queries, sensitive SQL parameters and all request/response payloads.
    Failure-audit unavailability is reported honestly, never as a durable record.

### Early shared implementation

- [ ] T011 [US4] Implement internal atomic audit/error/telemetry enforcement shared primitives in `src/backend/Services/AtomicAudit.cs`, `Services/AuditWriter.cs`, `Api/ProblemResponses.cs` and `Services/SafeTelemetry.cs`; enforce the research decision for one SQL transaction and the model rule "Never return a successful unaudited mutation." [Issue](https://github.com/dmd0822/member-profile-demo/issues/41)
  - Dependency: T010. Execute BEFORE T005/T007/T009/T015/T017/T019 mutations.
    Integrate with T003's SQL session/correlation/collector seams; serially own
    these shared primitives. No queue, worker or external audit API.
  - Enforce AuditEvent IDs "Server-generated UUID PK"; actor
    "Restricted opaque FK AuditActor; no email, name, subject or token";
    operation "Allowlisted operation name, including compound verify/apply semantics";
    field names "Allowlisted field-name set; no before/after values"; time
    "Server UTC instant"; outcome "`Succeeded` or `Failed`"; failure category
    "Optional allowlisted nonpersonal failure category"; correlation
    "Server-controlled nonpersonal operation reference".
  - Follow model rollback/separate failure-audit boundary and restricted
    append-only audit access. Implement safe JSON problem responses and no-store.
    Enforce server-only Application Insights Entra ingestion/local auth disabled;
    suppress/redact automatic URL/body/SQL collection before enabling it.
    No JS SDK/browser proxy/local-auth workaround.

## Phase 7: US5 — Use Key Flows Accessibly (P1)

**Goal**: Reusable keyboard/semantic/error behavior with early hosted-OTP review.
**Independent test**: Keyboard-operate reusable controls and mocked async states;
inspect labels/errors/focus/announcements and hosted paste. Final acceptance must
cover every actual member/staff flow, not only component tests.

### Tests before implementation

- [ ] T012 [P] [US5] Write fail-first keyboard/labels/errors/OTP-paste common-controls tests in `tests/unit/frontend/semantic-controls.spec.ts` and document hosted-flow checks in `specs/002-member-profile-demo/quickstart.md`; preserve the model constraint "Required input must be present, non-null and nonblank". [Issue](https://github.com/dmd0822/member-profile-demo/issues/40)
  - Dependencies: T001 and 003 T012 / issue 30. Tests target disjoint common
    control files; no dependency on T003 or implemented story pages.
  - Assert semantic labels/instructions, field-linked text errors, non-color
    feedback, visible focus, keyboard order, focus restoration, async status
    announcements, input preservation and OTP paste. Record the approved
    assistive/browser tooling and manual hosted-flow review; do not claim a unit
    test proves provider accessibility or broader WCAG certification.

### Early shared implementation

- [ ] T013 [US5] Implement reusable semantic controls/focus/status/error foundation and early hosted OTP review in `src/frontend/src/app/components/forms/`, `components/status/`, `services/navigation-focus.service.ts` and `specs/002-member-profile-demo/quickstart.md`; preserve "Do not copy any of them to telemetry, fixtures containing real people, or AI prompts." [Issue](https://github.com/dmd0822/member-profile-demo/issues/47)
  - Dependencies: T003, T012. Provide native form controls, visible matching
    labels/instructions, field errors/summary, keyboard focus and status behavior
    from `contracts/ui-flows.md`. No unrequested component framework or arbitrary
    live-region/focus manipulation. Keep sensitive view state out of persistent
    browser storage and clear on logout/account switch.
  - Review the actual configured hosted OTP entry early, including paste,
    invalid/expired code errors and keyboard operation, with approved test
    accounts only. Missing tenant/tool access remains an explicit blocker, not
    permission to replace hosted identity or simulate a passing manual review.
    All later story UIs consume this foundation.

## Phase 8: US6 — Maintain and Find Profiles (P2)

**Goal**: Staff create/read/update/search without changing sign-in associations.
**Independent test**: Fixture profiles and memberships; all four search categories,
generated unique numbers, five-field edits, empty results and nonstaff denial.

### Tests before implementation

- [ ] T014 [P] [US6] Write staff profile/search tests and 4-route contracts in `tests/unit/frontend/staff-profiles.spec.ts`, `tests/integration/backend/StaffProfileTests.cs` and `tests/contract/backend/StaffProfileContractTests.cs`; assert the model rule "No shared email/name implies a relationship." [Issue](https://github.com/dmd0822/member-profile-demo/issues/49)
  - Dependency: T003. R10–R13; fixture memberships support membership-number
    search without requiring T017. Cover generated-number collisions, create/read/
    update each profile field, all four staff-only searches and literal substring/
    exact-number semantics, empty/paged results, safe input and no value leakage.
    Missing/stale ETag, cross-role denial, overposting and audit rollback required.
  - Enforce profile member number
    "Application-generated immutable nonblank string, unique"; five field
    strings/date "Required Unicode strings"/"Required date"; no link/sign-in/
    member-number editing. Search cannot substitute for staff verification.

### Implementation

- [ ] T015 [US6] Implement staff profile maintenance/search API/UI in `src/backend/Api/StaffProfileEndpoints.cs`, `Services/StaffProfileService.cs`, `src/frontend/src/app/services/staff-profile.service.ts` and `pages/staff-profiles/`; preserve "Search cannot bypass staff authorization or leak through request logs." [Issue](https://github.com/dmd0822/member-profile-demo/issues/46)
  - Dependencies: T011, T013, T014. R10–R13; five-field profile create/patch,
    generated immutable unique member number; staff-only search by member number,
    membership number, name/contact email. Use reviewed bounds/collation/indexes,
    parameterized access, protected POST search body and no body/query telemetry.
  - Preserve model "No deletion/inactive profile workflow is invented."
    Required profile strings/date and rowversion remain enforced; no email
    uniqueness/autolink. Reuse profile record from T007 if already present, or
    serially implement it from the same model if this branch executes first.
    Shared-file implementation tasks must not run concurrently.

## Phase 9: US7 — Maintain Memberships and History (P2)

**Goal**: Multiple uniquely numbered memberships, valid dates and retained ended
history. **Independent test**: Fixture profile; add/edit/end memberships and
read their numbers/dates with optional end dates; reject invalid dates/nonstaff.

### Tests before implementation

- [ ] T016 [P] [US7] Write membership tests and 4 staff-route contracts in `tests/unit/frontend/staff-memberships.spec.ts`, `tests/integration/backend/MembershipTests.cs` and `tests/contract/backend/MembershipContractTests.cs`; assert "No inferred rule requires an end date for `Demo ended`, auto-ends by date, auto-generates an end date, or forbids changing placeholder statuses." [Issue](https://github.com/dmd0822/member-profile-demo/issues/48)
  - Dependency: T003. R14–R17; fixture profile avoids upstream implementation
    dependency. Test unique number races, multiple/ended history and nested
    profile mismatch, role/ETag failures, invalid/missing fields, audit rollback.
  - Exact type constraint "Exactly `Demo type A` or `Demo type B`"; status
    "Exactly `Demo active` or `Demo ended`"; start "Required date"; end
    "Nullable date; SQL check EndDate is null or EndDate >= StartDate".
    Ownership "Required FK MemberProfile; immutable ownership"; number
    "Application-generated immutable nonblank string, unique among memberships".

### Implementation

- [ ] T017 [US7] Implement staff membership/history API/UI in `src/backend/Models/Membership.cs`, `Api/StaffMembershipEndpoints.cs`, `Services/MembershipService.cs` and `src/frontend/src/app/pages/staff-memberships/`; preserve "Numbers are unique in their category, not necessarily across profile and membership tables." [Issue](https://github.com/dmd0822/member-profile-demo/issues/50)
  - Dependencies: T011, T013, T015, T016. R14–R17; generate membership UUID/
    immutable unique number; profile FK ownership immutable. Apply exact demo
    enums, required start/optional valid end, SQL date check, UTC created time
    and rowversion. Return ended records with numbers/dates; no deletion.
  - Enforce model check "Nullable date; SQL check EndDate is null or EndDate >= StartDate".
    Ending does not require an end date; these are not production status rules.
    Member-read routes are integrated in T009; do not add direct member writes.

## Phase 10: US8 — Request and Record Membership Changes (P2)

**Goal**: Submitted, simulated review, approved/rejected and explicit once-only
application with visible outcomes. **Independent test**: Fixture verified member/
membership/request states; separate submit/review/apply and inject each failure.
Final end-to-end integration uses real earlier story paths.

### Tests before implementation

- [ ] T018 [P] [US8] Write requests tests and 7 remaining request-route contracts in `tests/unit/frontend/membership-requests.spec.ts`, `tests/integration/backend/RequestWorkflowTests.cs` and `tests/contract/backend/RequestContractTests.cs`; assert "Rejected and Applied are terminal for this demo." [Issue](https://github.com/dmd0822/member-profile-demo/issues/45)
  - Dependency: T003. R18–R24; use fixtures for ownership/membership/lifecycle.
    Submit/approval must not mutate membership; failed simulated handoff remains
    visibly simulated, no external approval. Test both decisions, illegal/
    terminal transitions, malformed/unknown approved fields, stale request/
    membership versions, cross-member request reads, audit rollback and repeated/
    concurrent apply, including direct staff edits after approval.
  - Verbatim constraints: desired change
    "Required plain text description; sensitive; never an executable command";
    state "`Submitted`, `Approved`, `Rejected`, `Applied`"; handoff
    "Constant true" and "`Succeeded` or `Failed`; never means externally approved".
    ApprovedChange "must contain at least one proposed membership field".
    "Explicit null clears EndDate; omission means unchanged."
    "No ID/number/owner may be included."

### Implementation

- [ ] T019 [US8] Implement simulated request review/application API/UI in `src/backend/Models/MembershipChangeRequest.cs`, `Api/RequestEndpoints.cs`, `Services/RequestWorkflowService.cs`, `src/frontend/src/app/services/membership-requests.service.ts` and `pages/membership-requests/`; preserve "A stale membership version leaves the request Approved and reports conflict." [Issue](https://github.com/dmd0822/member-profile-demo/issues/55)
  - Dependencies: T007, T009, T011, T013, T017, T018. R18–R24; store UUID,
    requester identity FK, matching profile/membership relationship, nonblank
    sensitive description, server SubmittedAt and rowversion; resolve current
    verified ownership on every read. No requester-selected actor/status.
  - Enforce state "`Submitted`, `Approved`, `Rejected`, `Applied`"; handoff
    "Constant true", outcome "`Succeeded` or `Failed`; never means externally approved";
    failure code "Nullable allowlisted nonpersonal code, not exception/provider text".
    Decision actor/time "Null until decision, restricted actor FK and UTC time after decision";
    ApprovedChange/ApprovedMembershipVersion "Null unless approved/applied";
    Applied actor/time "Null except Applied; restricted actor FK and UTC time".
  - Freeze an exact reviewed MembershipPatch with presence markers and captured
    membership version; approve/reject only Submitted, then explicit bodyless
    apply only Approved. "No ID/number/owner may be included." Validate whole
    resulting membership against the exact T016/T017 fields/date rules.
  - Membership update, Applied metadata and audit commit together. Failed apply
    preserves Approved/unchanged membership. Prior handoff failure stays visible;
    record its failed audit alongside successful request creation. No external
    service, worker/queue, implicit approval or unreviewed retry.
    Direct staff maintenance cannot mark a request Applied. Stale approved
    payloads need a newly reviewed request, not silent replacement.

## Phase 11: Validation, documentation and completion

**Goal**: Measured evidence and accurate delivered documentation, not assumed
readiness. No provisioning/deployment authority is granted by validation tasks.

- [ ] T020 Record PO workload agreement and basis in `specs/002-member-profile-demo/plan.md` and its validation reference in `quickstart.md`; preserve the model privacy constraint "Operational telemetry has only allowlisted nonpersonal operation/result/duration/correlation metadata." [Issue](https://github.com/dmd0822/member-profile-demo/issues/53)
  - Dependencies: none; requires actual PO input, independently of implementation.
    Agree expected concurrency/request rate, journey mix, dataset volume/duration,
    measurement boundary, cold/resume treatment and supporting basis before
    NFR-001/SC-008 measurement. No invented numbers, rate limits or passing result.

- [ ] T021 Perform approved environment access/security, actual-provider transaction/concurrency/private-data and agreed-load performance validation in `tests/integration/backend/EnvironmentValidationTests.cs`, `tests/integration/backend/SqlConcurrencyValidationTests.cs`, `tests/integration/backend/PerformanceValidationTests.cs` and `specs/002-member-profile-demo/quickstart.md`; preserve "Private SQL connectivity, approved US residency, encryption/key decisions, managed-identity permissions and production retention/deletion policy require the reviews in [plan](plan.md)." [Issue](https://github.com/dmd0822/member-profile-demo/issues/56)
  - Dependencies: T019, T020, plus an explicitly approved pre-existing environment.
    NO provisioning, public SQL fallback, test/prod portal changes or deployment.
    Missing resource/network/tenant/tool/permission approval is a blocker.
  - Validate real Entra member/workforce behavior and hosted OTP failure/paste;
    private endpoint/DNS/public-access-disabled SQL, resource-scoped managed
    identity, approved US service/data location, TLS/encryption and key decisions,
    safe server telemetry ingestion with local auth disabled. No JS workaround.
  - Exercise actual SQL rowversion, unique number/link races, approved membership
    concurrency and atomic mutation/request/audit rollback, failed-attempt audit
    availability and ambiguous-commit readback. A fake provider is not evidence.
  - Measure p95 API latency under 300 ms only at T020's agreed workload/basis/
    boundary; report cold/resume and blocked/failed results honestly. Do not
    invent production retention or recovery policy to make a check pass.

- [ ] T022 Establish >=80% business-logic coverage, full 24-route contract matrix and executable CI/SAST/secret/dependency integration in `tests/contract/backend/`, `tests/unit/backend/`, `tests/unit/frontend/`, `.github/workflows/application-ci.yml`, `.github/scripts/ci-foundation.mjs` and reviewed coverage/scan configuration; preserve "SQL primary keys, foreign keys and unique constraints are authoritative." [Issue](https://github.com/dmd0822/member-profile-demo/issues/52)
  - Dependency: T019. Run all T004/T006/T008/T014/T016/T018 route suites and
    T002/T010/T012 cross-cutting assertions with nonzero discovery and failure
    propagation. Every R01–R24 requires positive/negative policy/shape/correlation/
    no-store tests; every mutation atomic audit; every conditional write
    missing/stale/valid ETag. Report SQL-environment evidence separately in T021.
  - Measure >=80% line coverage on business logic, not documentation/file counts.
    Integrate real build/test/contract/coverage/SAST/secret/dependency commands in
    existing application CI, preserve stable required result names and separate
    infrastructure CI, and never skip dependent checks due to path filters.
    No CD, secrets/cloud keys, public non-npm fallback or deployment.
    Unavailable scanners/feed access are blockers, never successful checks.

- [ ] T023 Update delivered docs/contracts/README alongside the implementation in `README.md`, `docs/architecture/member-profile-overview.md`, `docs/architecture/design-review.md`, `docs/architecture/azure-infrastructure.md` and `specs/002-member-profile-demo/{plan.md,research.md,data-model.md,quickstart.md,contracts/}`; preserve "These are demo labels, not production policy." [Issue](https://github.com/dmd0822/member-profile-demo/issues/54)
  - Dependencies: T019, T021, T022. Record actual project/config/setup/run/test
    paths/commands, reviewed dependencies, ownership/privacy/audit/concurrency,
    synthetic seed/reset, limits and validation outcomes. Review changed shared
    contract generation only if actual reuse requires it. Do not create a C#
    shared implementation library for Angular.
  - Reconcile older queue/worker/central-audit and browser-telemetry/hosting
    directions with actual approved decisions; never mark an ADR accepted without
    recorded review. Keep approved proposal versus implemented state distinct.
    Cover changed setup/usage/API/configuration/structure/deployment guidance in
    the same PR; identify unchanged areas and reasons. No invented working commands,
    production policy or environment readiness.

- [ ] T024 Execute all-eight-story final acceptance/convergence/status synchronization using `specs/002-member-profile-demo/spec.md`, `quickstart.md`, `tasks.md`, `tests/contract/backend/` and `tests/integration/backend/`; preserve "These are future test obligations, not executed results." until evidence actually exists. [Issue](https://github.com/dmd0822/member-profile-demo/issues/51)
  - Dependencies: T021, T022, T023. Demonstrate every acceptance scenario and
    SC-001–SC-008, both verification paths, all roles, privacy/audit/rollback,
    optional/invalid dates/ended history and every request outcome/failure.
    Re-run keyboard/assistive tests on every actual member/staff flow.
  - Use the approved convergence workflow for read-only assessment first;
    no extra task IDs or scope expansion without review. Resolve findings through
    existing task scopes or report a separate scope decision.
    Synchronize checkboxes/issues/milestone from actual evidence only. Required
    human PR approval and passing executable CI remain mandatory; no self-approval,
    deployment, or claim that planning/task generation completes the feature.

## Dependencies and execution order

The table gives **direct dependencies exactly**. Transitive prerequisites remain
in force; shared implementation files are serialized even when independent
branches of the graph are ready.

<!-- prettier-ignore -->
| Task | Direct task dependencies | Additional gate |
| --- | --- | --- |
| T001 | None | Actual named human review; no foundation needed to review |
| T002 | T001 | Reviewed compiling test harness; external foundation gate below |
| T003 | T002 | Same backend only; no story mutation before T011 |
| T004 | T003 | Disjoint tests |
| T005 | T004, T011, T013 | Shared audit and UI foundations |
| T006 | T003 | Disjoint tests |
| T007 | T005, T006, T011, T013 | Both verification paths |
| T008 | T003 | Synthetic memberships allowed in tests |
| T009 | T007, T008, T011, T013, T017 | T017 required for final membership-read integration |
| T010 | T003 | Synthetic operation drivers allowed |
| T011 | T010 | Must precede all story mutation implementations |
| T012 | T001 | Disjoint UI tests; external foundation gate below; no T003 prerequisite |
| T013 | T003, T012 | Early hosted OTP/accessibility review |
| T014 | T003 | Disjoint tests |
| T015 | T011, T013, T014 | Serial shared implementation |
| T016 | T003 | Disjoint tests |
| T017 | T011, T013, T015, T016 | Staff profile prerequisite |
| T018 | T003 | Disjoint tests |
| T019 | T007, T009, T011, T013, T017, T018 | Integrated request flow |
| T020 | None | Actual PO workload input |
| T021 | T019, T020 | Approved pre-existing environment; no provisioning |
| T022 | T019 | Approved SQL environment for real-provider tests; T021 separately records environment/performance evidence |
| T023 | T019, T021, T022 | Same-PR delivered docs |
| T024 | T021, T022, T023 | Evidence-based completion and synchronization |

**Cross-feature prerequisite outside the table**: T002 and T012 also depend on
feature 003's T012 ([issue 30](https://github.com/dmd0822/member-profile-demo/issues/30)).
The table's task tokens refer only to this feature. T001 and T020 do not require
that foundation to collect their human decisions.

### Story execution graph and incremental strategy

1. T001 review and T020 workload agreement can proceed independently.
   After 003 T012, write T002 and T012; T003 follows T002.
2. After T003, independent story test tasks T004/T006/T008/T010/T014/T016/T018
   can run on disjoint files. T012 may already be available.
3. Execute T011 and T013 early and serially as shared foundations.
   US1 implementation T005 is the smallest demonstrable entry slice, **not**
   completion of the full demo. It includes privacy/audit/accessibility.
4. US2 T007 follows US1. Separately, staff profiles T015 precede memberships T017.
   Serialize shared model/SQL/UI implementation edits. No independent task
   requires another implementation merely to author a synthetic fixture.
5. US3 T009 joins US2 and US7. US8 T019 joins verification, member read/contact
   and staff membership implementations. No cycle or new runtime is required.
6. T021 environment/performance and T022 CI/coverage both follow integrated US8;
   they are not `[P]` shared-implementation tasks. Final docs and acceptance follow
   the exact dependency table. No deploy/demo instruction overrides review gates.

### Parallel test opportunities by story

All examples are conditional on prerequisites and separate files, not permission
to edit shared configuration concurrently. Within one task the assigned owner
coordinates its frontend/integration/contract tests; no extra IDs are introduced.

<!-- prettier-ignore -->
| Story | Eligible disjoint test task and example peer | Implementation constraint |
| --- | --- | --- |
| US1 | T004 EnrollmentContractTests.cs alongside T006 VerificationContractTests.cs after T003 | T005 waits for T011/T013 |
| US2 | T006 VerificationTests.cs alongside T008 MemberProfileTests.cs after T003 | T007 waits for T005 and common foundations |
| US3 | T008 MemberProfileTests.cs alongside T016 MembershipTests.cs using independent fixtures | T009 waits for T017 final integration |
| US4 | T010 AuditAtomicityTests.cs alongside T014 StaffProfileTests.cs after T003 | T011 is early, shared and serial |
| US5 | T012 semantic-controls.spec.ts alongside T002 foundation tests after T001/003 T012 | T013 also waits for T003; all story UIs consume it |
| US6 | T014 StaffProfileContractTests.cs alongside T018 RequestContractTests.cs after T003 | T015 serializes shared profile schema edits |
| US7 | T016 MembershipContractTests.cs alongside T004 EnrollmentContractTests.cs after T003 | T017 waits for T015; no production status rules |
| US8 | T018 RequestWorkflowTests.cs alongside T008 MemberProfileTests.cs after T003 | T019 integrates prerequisites, never parallel shared writes |

## Complete public route coverage inventory

All paths below have the `/api/v1` prefix. Every route is covered by the named
story test/implementation pair plus T010/T011/T022/T024 cross-cutting checks.
`Mutation` means successful domain state and its audit must commit together.
Collections include ended history where applicable. These are proposed routes,
not evidence that any endpoint exists.

<!-- prettier-ignore -->
| Route | Method and path | Story test -> implementation | Mutation |
| --- | --- | --- | --- |
| R01 | POST `/me/enrollment` | T004 -> T005 | Yes |
| R02 | GET `/me/enrollment` | T004 -> T005 | No |
| R03 | GET `/staff/enrollments` | T006 -> T007 | No |
| R04 | GET `/staff/enrollments/{enrollmentId}` | T006 -> T007 | No |
| R05 | POST `/staff/enrollments/{enrollmentId}/verification` | T006 -> T007 | Yes |
| R06 | GET `/me/profile` | T008 -> T009 | No |
| R07 | PATCH `/me/profile` | T008 -> T009 | Yes |
| R08 | GET `/me/profile/memberships` | T008 -> T009 | No |
| R09 | GET `/me/profile/memberships/{membershipId}` | T008 -> T009 | No |
| R10 | POST `/staff/profiles` | T014 -> T015 | Yes |
| R11 | POST `/staff/profile-search` | T014 -> T015 | No, read-only search |
| R12 | GET `/staff/profiles/{profileId}` | T014 -> T015 | No |
| R13 | PATCH `/staff/profiles/{profileId}` | T014 -> T015 | Yes |
| R14 | GET `/staff/profiles/{profileId}/memberships` | T016 -> T017 | No |
| R15 | POST `/staff/profiles/{profileId}/memberships` | T016 -> T017 | Yes |
| R16 | GET `/staff/profiles/{profileId}/memberships/{membershipId}` | T016 -> T017 | No |
| R17 | PATCH `/staff/profiles/{profileId}/memberships/{membershipId}` | T016 -> T017 | Yes |
| R18 | POST `/me/profile/memberships/{membershipId}/requests` | T018 -> T019 | Yes |
| R19 | GET `/me/requests` | T018 -> T019 | No |
| R20 | GET `/me/requests/{requestId}` | T018 -> T019 | No |
| R21 | GET `/staff/requests` | T018 -> T019 | No |
| R22 | GET `/staff/requests/{requestId}` | T018 -> T019 | No |
| R23 | POST `/staff/requests/{requestId}/decision` | T018 -> T019 | Yes |
| R24 | POST `/staff/requests/{requestId}/application` | T018 -> T019 | Yes |

Contract tests for each route include successful shape and wrong/missing token,
issuer, scope, staff entitlement/verification and inaccessible resources as
applicable; no-store and safe correlation; invalid/unknown fields for input;
missing/stale/valid If-Match for every conditional mutation. T021 proves actual
provider/SQL constraints; T022 proves full matrix/CI execution. No contract test
may treat a missing unbuilt route as passing behavior.

## Requirement traceability

This single table covers **all 17 FRs, NFR-001 and all eight SCs**. Every row
has tests and delivery/validation owners; references to T001 mean decisions
are required, not self-approved.

<!-- prettier-ignore -->
| Requirement | Tests and implementation/validation tasks |
| --- | --- |
| FR-001 | T001, T002, T003, T004, T005, T006, T007, T012, T013, T021, T024 |
| FR-002 | T002–T009, T014–T019, T021, T022, T024 |
| FR-003 | T004, T005, T022, T024 |
| FR-004 | T006, T007, T022, T024 |
| FR-005 | T002, T004–T007, T021, T022, T024 |
| FR-006 | T006, T007, T014, T015, T021, T022, T024 |
| FR-007 | T008, T009, T016, T017, T022, T024 |
| FR-008 | T002, T004, T005, T008, T009, T014, T015, T024 |
| FR-009 | T016, T017, T018, T019, T021, T022, T024 |
| FR-010 | T008, T009, T016, T017, T023, T024 |
| FR-011 | T008, T009, T018, T019, T022, T024 |
| FR-012 | T018, T019, T023, T024 |
| FR-013 | T018, T019, T021, T022, T024 |
| FR-014 | T002, T003, T010, T011, T005, T007, T009, T015, T017, T019, T021, T022, T024 |
| FR-015 | T001, T002, T003, T010, T011, T012, T013, T020, T021, T022, T023, T024 |
| FR-016 | T002, T003, T010, T011, T018, T019, T021, T022, T024 |
| FR-017 | T012, T013, T005, T007, T009, T015, T017, T019, T021, T024 |
| NFR-001 | T020, T021, T023, T024 |
| SC-001 | T004–T019, T021, T022, T023, T024 |
| SC-002 | T002–T009, T014–T019, T021, T022, T024 |
| SC-003 | T010, T011, T018, T019, T021, T022, T024 |
| SC-004 | T006–T009, T014–T019, T021, T022, T024 |
| SC-005 | T002, T003, T010, T011, T021, T022, T024 |
| SC-006 | T004–T019, T023, T024 |
| SC-007 | T012, T013, T005, T007, T009, T015, T017, T019, T021, T024 |
| SC-008 | T020, T021, T023, T024 |

## Completion evidence and scope limits

Task count: **24**. Each US1–US8 has exactly **two** tasks (one tests, one
implementation); setup/foundation has three; final validation/docs has five.
Eight disjoint story test tasks carry `[P]`; shared implementations do not.
The minimal initial member slice is US1 plus early US4/US5 foundations, not
permission to omit the remaining seven stories.

Every task has exactly one actual issue link on its checklist line; all are open.
Do not mark tasks complete because planning artifacts, tests without execution,
or proposed approvals exist. Follow the [quickstart](quickstart.md), approved
review gates, required executable CI and human PR review. No installation,
application implementation, GitHub mutation, commit, provisioning or deployment
is performed by generating this backlog.
