# Research: Member Profile Demo

**Date**: 2026-11-16. **Status**: Proposed design inputs for formal planning.
The spec is approved for planning only; no design is accepted by this record.
Audience: implementation planners and human architecture/security/privacy
reviewers. See [plan](plan.md) for gates and [spec](spec.md) for requirements.

## Evidence and clarification assessment

Read the constitution v2.3.0, repository and applicable instructions, README,
002 spec/checklist, 003 spec and in-progress foundation plan/research, root
tooling, and the three Proposed architecture documents. No accepted ADR directory
was present. The architecture discovery was already completed
by Member Services Azure Architect; its supplied findings are reused here.
No duplicate architecture discovery or new external research agent was run.

The installed `speckit-clarify` workflow was invoked with explicit 002 feature
selection and no persistence. Its prerequisite script resolved
`specs/002-member-profile-demo/spec.md`. No critical ambiguities detected worth
formal clarification. Zero questions asked/answered; no spec or checklist
sections touched. Read-only checklist reassessment: **15/16 -> 15/16** passing,
no regressions/new checks; demonstrated outcomes remain unchecked because no
application exists. No `.specify/extensions.yml` exists, so pre/post clarification
and planning hooks are inapplicable.

<!-- prettier-ignore -->
| Clarification category | Status | Assessment |
| --- | --- | --- |
| Functional scope/behavior | Clear | Eight stories and exclusions bound the complete synthetic demo. |
| Domain/data | Clear | Required fields, ownership, unique identifiers and request lifecycle are stated; physical schema is a design choice below. |
| Interaction/UX | Clear | Critical paths and accessibility acceptance defined; screen and HTTP details are proposed contracts. |
| Nonfunctional quality | Deferred | Security/privacy/audit controls clear; PO workload agreement required before performance validation. No availability or retention policy invented. |
| Integration/dependencies | Deferred | Review is explicitly simulated; Entra configuration, package and environment choices require technical review, not new product scope. |
| Edge cases/failures | Clear | Missing fields, date order, link conflicts, authorization, handoff/apply/audit failures covered. Concurrency details proposed below. |
| Constraints/trade-offs | Clear | Constitution fixes stack, US-only/private identity-based access and test-first obligations. |
| Terminology | Clear | Identity/contact email separate; profile/member number distinct from membership number; simulated review explicit. |
| Completion signals | Clear | SC-001–SC-008 measurable after implementation and agreed load. |
| Miscellaneous/placeholders | Clear | Demo labels are intentional; no open requirements clarification marker. |

Deferred items are not hidden blocking product questions. Human design approval
must precede sensitive implementation; workload agreement precedes its relevant
validation. No requirement was silently added to the spec.

## Decision 1: Reuse the foundation; one API runtime

**Decision (proposed)**: Angular/CLI 22.x, Node.js >=24.15.0 <25,
TypeScript 6.0.x, RxJS 7.8.x, .NET 11. Reuse 003's one frontend and one
host-neutral backend project. Convert that same backend project to a conventional
ASP.NET Core API executable only after 002 review. Keep HTTP concerns in `Api/`,
records in `Models/`, business/SQL/audit operations in `Services/`.

**Rationale**: Browser/member/staff operations share one transactional data
boundary. The API shared framework is appropriate for request validation,
policy authorization and consistent errors. A foundation library is not an
already-operational server. Toolchain compatibility follows the official
[Angular version table](https://angular.dev/reference/versions);
003's research independently records that baseline. Official
[.NET 11 downloads](https://dotnet.microsoft.com/en-us/download/dotnet/11.0)
identify 11.0.100 release availability; exact package pins and installed SDK
remain unverified. See [ASP.NET Core APIs](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/apis?view=aspnetcore-11.0).

**Alternatives considered**: A second API project would contradict the one-backend
boundary without need. Microservices, mediator/repository frameworks and a worker
add coordination not required by the demo. Azure Functions is constitutionally
available but provides no demonstrated advantage for this synchronous SQL-bound
API. Container Apps is a hosting candidate only; do not manufacture a hosting
decision, Dockerfiles or Bicep here.

**Review gate**: Tech lead reviews same-project conversion, endpoint design,
compatible exact SDK/packages and external test discovery. Resolve hosting,
network/identity and cost implications before environment-dependent validation.

## Decision 2: Hosted member email OTP and separate workforce staff trust

**Decision (proposed)**: Member sign-in uses Entra External ID in an external
tenant with hosted email one-time-passcode as the primary sign-in method.
Staff sign in with organization-managed workforce accounts. The same API uses
separate explicitly configured issuer/audience/scope policies. Workforce-only
designated staff entitlement must never be accepted from member-issued claims.

**Rationale**: The official
[external-tenant authentication methods](https://learn.microsoft.com/en-us/entra/external-id/customers/concept-authentication-methods-customers)
document email OTP as primary authentication, distinct from an MFA-only step.
This supports feasibility, not configuration approval. Let the provider own code
generation, expiry, replay prevention and hosted paste-capable entry; do not
build a local OTP service. Validate API access tokens, not ID tokens: signature,
trusted exact issuer, API audience, lifetime and required scopes using supported
libraries. Official [access-token guidance](https://learn.microsoft.com/en-us/entra/identity-platform/access-tokens)
supports resource-side validation.

**Alternatives considered**: Workforce guest-email OTP does not establish the
intended customer sign-in model. Custom email-code storage/token issuance,
local test auth bypass, shared API keys, or accepting email as identity violate
the trust/security constraints. Do not add a BFF, cookie server or second runtime
without a concrete reviewed need; proposed browser access tokens remain in
memory, not URLs or unsafe persistent storage.

**Review gate**: Security champion/tech lead review tenant configuration, exact
trust policy and entitlements, consent/scopes, redirect and CORS allowlists,
token lifecycle/logout, SDKs and hosted accessibility. Privacy review confirms
identity service data location and lifecycle. No Entra resources are configured
here and no authentication tests have run.

## Decision 3: Immutable identity, verified links and narrow DTOs

**Decision (proposed)**: Key members by the trusted immutable issuer/subject pair;
never by email. Store a restricted mapping to an opaque internal identity and
audit actor. Staff verification transactionally links one identity to one
profile. Database uniqueness prevents competing profile claims. Staff create
profiles without links; an applicant presenting matching contact details or
numbers gains no access.

**Rationale**: FR-002–FR-008 require pending isolation and unchanged association
when contact email changes. Email and generated numbers are lookup attributes,
not authorization. Derive ownership from the authenticated identity and verified
link for every nested route. Separate member contact, staff profile, membership
and review DTOs prevent privileged-field mass assignment.

**Alternatives considered**: UI-only guards, caller-supplied actor/profile IDs,
auto-link by email/number, or shared unfiltered persistence DTOs would leak or
mutate data across the verification boundary. No email uniqueness is inferred.

**Review gate**: Security/privacy reviewers approve link invariants, actor
mapping access and deletion/retention implications. Opaque actor IDs are
privacy-sensitive pseudonymous references, not anonymous telemetry labels.

## Decision 4: SQL transactions and optimistic concurrency

**Decision (proposed)**: Azure SQL stores the domain and its internal audit.
Use primary/foreign keys, unique constraints and rowversion. EF Core 11's SQL
provider is the proposed data-access dependency, subject to approval. API ETags
encode rowversions; state-changing resource updates require `If-Match`.

**Rationale**: Database constraints arbitrate concurrent verified links and
generated-number collisions. A request can be applied only once using an atomic
approved-state check plus membership/request versions. A stale membership after
approval requires re-evaluation, not blind overwrite. Server-generated numbers
are opaque, immutable and unique within their own categories; do not imply
contiguous or chronological identifiers.
See [EF transactions](https://learn.microsoft.com/en-us/ef/core/saving/transactions)
and [optimistic concurrency](https://learn.microsoft.com/en-us/ef/core/saving/concurrency).

**Alternatives considered**: Last-write-wins loses updates. Client uniqueness
checks cannot arbitrate races. Direct parameterized SQL is viable and has fewer
ORM abstractions, but requires manual concurrency/transaction mapping; choose it
if the tech lead rejects the provider dependency, preserving all invariants.
An in-memory store cannot prove private SQL, rowversion or transactional behavior.

**Review gate**: Tech lead approves provider, number strategy, schema bounds,
DTO serialization, status conventions and concurrency. Retries must not
re-execute an ambiguously committed mutation; no automatic replay is promised.

## Decision 5: Failure-safe internal audit, not a queue integration

**Decision (proposed)**: A successful domain mutation and its internal audit
record commit in one SQL transaction. If audit insert or transaction commit
fails, report failure and commit no successful unaudited state. After a denied,
invalid or rolled-back authenticated attempt, append a separate minimal failure
event if the audit store is available. If unavailable, return a safe failure
with correlation and no business mutation.

**Rationale**: FR-014 asks for internal actor/action/time/outcome records without
profile values. SQL supplies the needed atomic boundary, including membership
application plus request-state update plus audit. Audit records contain operation
and field names, never requested descriptions or before/after values.

**Alternatives considered**: Logging as audit is not durable atomic business
history. Queue then SQL, or SQL then queue, are not atomic together. The
older Proposed documents' Storage Queue, central audit API, delivery worker and
three-hour alert are deferred beyond the demo, not required dependencies.
If central delivery becomes required later, review an outbox/deduplication and
recovery design separately; do not claim exactly-once cross-store delivery.

**Review gate**: Tech lead/security/privacy review all successful and failed
write paths, audit access restrictions and fault injection. There is no public
audit endpoint or member-facing audit history.

## Decision 6: Honest simulated review and explicit application

**Decision (proposed)**: Requests preserve a member's desired-change description.
Submission records `Submitted` and a separately labeled simulated handoff
outcome; no membership is changed. Staff explicitly record `Approved` with the
exact proposed field changes or `Rejected`. Approval captures the membership
version and freezes the apply payload. A later explicit apply updates membership,
request to `Applied` and audit together.

**Rationale**: FR-011–FR-013 separate submission, recorded review and application.
Free text is not executable policy. The proposed structured staff payload makes
the approved change testable without requiring members to know backend field
names. A failed apply leaves `Approved`, not `Applied`, and preserves membership.
A stale version is visible and requires a newly reviewed decision, not a silent
rewrite. A rejected or applied request cannot be reused.

**Alternatives considered**: Parsing prose into automatic writes, applying at
approval time, or showing a handoff acknowledgment as approval contradicts the
spec. A real external review integration is out of scope. Approval by a different
staff person is not an invented requirement: separation is simulated and labeled.

**Review gate**: PO/tech lead confirm the proposed review payload and stale-
approval recovery presentation. This plan adds no production eligibility,
status-transition or date rules. Failed handoff never creates an approval;
staff may subsequently explicitly record a simulated review, with the original
handoff failure still visible.

## Decision 7: Server-only compliant telemetry

**Decision (proposed)**: Emit allowlisted server logs, traces and metrics through
supported Entra-authenticated Application Insights ingestion with local
authentication disabled. Use route templates, operation names, duration,
result and correlation; omit identity/actor IDs, raw paths/queries, request/
response bodies, profile values, codes, tokens and sensitive SQL parameters.
Disable/redact automatic collectors before enabling them.

**Rationale**: Official
[Application Insights Entra authentication](https://learn.microsoft.com/en-us/azure/azure-monitor/app/azure-ad-authentication)
states that the JavaScript SDK does not support Entra-authenticated ingestion.
The old proposal's direct UI-to-Insights arrow is therefore not a compliant
browser implementation. Server telemetry meets the demo's observability boundary;
opaque audit actors stay restricted in SQL, not telemetry.

**Alternatives considered**: Enabling local auth, a browser ingestion key,
forwarding arbitrary browser payloads, or an unaudited browser-telemetry proxy
would weaken security or add unnecessary scope. No browser workaround is selected.

**Review gate**: Security/privacy approve sanitized automatic collection and
resource-scoped ingestion identity. Validate safe correlation, query-string
suppression on staff search and failure telemetry without member payloads.

## Decision 8: External tests and staged validation

**Decision (proposed)**: Reuse 003's proposed Vitest/jsdom and MSTest families;
keep all unit/integration/public API contracts in `tests/`. Test fail-first,
80% minimum business-logic line coverage, every public route's contract and
cross-member/staff denials. Use provider-backed transaction/concurrency tests
and reviewed browser/assistive technology validation.

**Rationale**: Current root scripts validate documentation and tooling only.
They cannot certify a nonexistent Angular/API/SQL application. Environment
identity/private SQL and telemetry checks need separately approved resources.
NFR-001's peak workload is unknown and must be agreed before performance tests,
not invented to make planning appear complete.

**Alternatives considered**: Co-located tests violate repository layout;
in-memory-only SQL tests miss constraints; automated accessibility checks alone
cannot prove keyboard/assistive behavior. Fixed rate limits, production retention,
availability objectives and load numbers are not inferred from generic guidance.

**Review gate**: Review exact test packages, internal non-npm feed, browser tooling
and runner access. Separate app CI activation from infrastructure CI; required
SAST, secret and dependency scans need executable evidence, not documentation.

## Verification limits and follow-up

Official URLs above are the sources supplied by completed architecture discovery
and 003 research. This authoring phase did not refetch them or independently
exercise service behavior. No private code or data was sent to external research.
No application dependency was installed; no source, infrastructure or GitHub
resource was changed. Planning docs are not proof of working authentication,
SQL transactions, telemetry ingestion, accessibility or performance.

Plan review must reconcile the old Proposed architecture's queue and hosting
language and browser telemetry with this minimal proposal. Production policies
remain out of scope. The [implementation backlog](tasks.md) now records exactly
T001–T024 with actual open issue links under the milestone in [plan](plan.md).
Its explicit dependencies put shared audit and accessibility foundations before
story implementations, permit isolated fixture-based fail-first test authoring,
and gate provider/performance validation on approved resources and PO workload.
T001 records review of all newly proposed schema/HTTP/bounds choices; task
generation does not accept them. Read-only `/speckit-analyze` follows generation;
no implementation or completed acceptance result is implied.
