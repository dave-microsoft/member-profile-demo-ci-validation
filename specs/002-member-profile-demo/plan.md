# Implementation Plan: Member Profile Demo

**Branch**: `dave-microsoft-spec-project-planning` (assigned checkout) | **Date**: 2026-11-16 | **Spec**: [Member Profile Demo](spec.md)

**Input**: `specs/002-member-profile-demo/spec.md`.
Explicit feature target: `SPECIFY_FEATURE_DIRECTORY='specs/002-member-profile-demo'`,
`SPECIFY_FEATURE_NO_PERSIST='1'`. Spec Kit reports the feature identifier
`002-member-profile-demo`; it is not the physical checkout branch.

**GitHub Milestone**: [Member Profile Demo milestone 3](https://github.com/dmd0822/member-profile-demo/milestone/3).
The [implementation backlog](tasks.md) records T001–T024 and their actual open
issue links from the supplied map. Keep milestone/issue dependencies, scope and
completion evidence synchronized; creating issues does not complete the work.

**Status**: Proposed design, not implementation-ready. The user acting as product
owner and tech lead approved the spec for planning on 2026-11-16; design,
security, and privacy approval are still required. Planning approval and this
document do not authorize application code, dependencies, infrastructure, or
deployment. Phase 0/1 design is followed by the proposed Phase 2 backlog and
read-only consistency analysis; implementation remains gated.

## Summary

Deliver the complete synthetic-data demo across all eight stories: hosted
email-code member authentication; enrollment and staff verification; member
contact self-service; staff profile search/maintenance; multiple memberships
and ended history; simulated request review and explicit approved-change
application; privacy-safe atomic audit; and accessible member/staff flows.

Propose one Angular application and one .NET 11 ASP.NET Core API with Azure SQL.
Reuse the [003 foundation](../003-initial-app-projects/plan.md); do not introduce
another backend project or runtime. Feature 003 proposes a host-neutral library,
not an already-running API. After 002 design approval, convert that **same**
backend project to an API executable. Hosting remains unselected; Container Apps
is a candidate, not an accepted decision.

Use the completed architect discovery, the
[research record](research.md), [data model](data-model.md),
[API proposal](contracts/api-v1.md), and [UI contract](contracts/ui-flows.md).
The older [architecture overview](../../docs/architecture/member-profile-overview.md),
[design review](../../docs/architecture/design-review.md), and
[infrastructure proposal](../../docs/architecture/azure-infrastructure.md)
remain Proposed. Their queue/central-audit and browser-telemetry directions are
not prerequisites of this demo. Record their reconciliation at human review;
do not treat those documents as approvals.

## Technical Context

**Language/Version**: Angular/CLI 22.x, TypeScript 6.0.x, RxJS 7.8.x,
Node.js >=24.15.0 <25; C# with .NET 11 SDK-compatible default language version,
target `net11.0`. Reuse 003's compatible baseline; select exact reviewed patches
and SDK pin before package introduction. .NET 11.0.100 release availability
does not prove local installation or package compatibility.

**Primary Dependencies**: Angular platform and its matching CLI/build tools;
ASP.NET Core shared framework for HTTP; proposed supported Entra browser/API
libraries, EF Core 11 SQL Server provider and managed-identity SQL authentication,
and supported server Application Insights/Azure Monitor instrumentation.
EF Core is justified for SQL constraints, tracked transactions, and optimistic
concurrency; direct parameterized SQL remains a simpler viable alternative if
package review rejects it. No repository framework, mediator, CQRS, custom
token/code service, queue, worker, component library, or extra runtime.
Exact identity, data-provider, telemetry and test packages require compatibility,
license, vulnerability and approved-feed review before introduction.

**Storage**: Azure SQL for identity-to-profile associations, enrollments,
profiles, memberships, requests, and internal audit. Business mutations and
required successful audit events share a single SQL transaction. No central
audit integration is needed for FR-014.

**Testing**: Proposed Vitest/jsdom aligned with 003 for frontend logic; proposed
MSTest for backend unit/integration/contract tests. Browser/assistive technology
tooling selection remains a review gate, not an installed dependency.
All tests live outside `src/`. Real SQL-provider integration is required to
validate transactions, uniqueness and rowversion; an in-memory substitute cannot
prove these. Contract-test every public endpoint. Tests precede implementation
and must fail first; minimum 80% business-logic line coverage.

**Target Platform**: Browser frontend, Azure-hosted API, private Azure SQL.
Member Entra External ID external-tenant hosted email OTP and separate workforce
staff trust are proposed. Validate access-token issuer, audience, lifetime and
required scopes; staff entitlement is accepted only from the workforce trust.
No local authentication bypass, custom OTP or API keys.

**Project Type**: One web application, one backend executable after approved
conversion of 003, language-neutral versioned contracts.

**Performance Goals**: NFR-001 / SC-008: p95 API latency under 300 ms at the
product-owner-agreed expected peak workload.

**Expected workload and basis**: Not yet agreed. Before performance validation,
the product owner must record expected concurrent actors/request rate, journey
mix, profile/membership/request volume, test duration and measurement boundary,
cold/resume treatment, and the evidence supporting that workload here.
These are measurement inputs, not invented targets. No performance pass is
claimed; unknown peak load does not block formal planning.

**Constraints**: Synthetic member records only; no PII/account data/payloads,
tokens or codes in telemetry, fixtures or AI prompts. Opaque actor references
are privacy-sensitive; mappings are restricted. TLS 1.2+, encryption at rest,
approved US regions, private SQL with public access disabled, resource-scoped
managed identity and Entra-authenticated telemetry with local auth disabled.
Application Insights JavaScript SDK does not support Entra ingestion: server
telemetry only, not a local-auth workaround.

**Scale/Scope**: All eight demo stories, FR-001–FR-017, NFR-001,
SC-001–SC-008. Production load, retention/deletion, minors, availability,
recovery targets, production verification and membership policies are not
selected by this plan. No real review integration or production deployment.

## Constitution Check

Gate assessment before Phase 0 and after Phase 1 is for **planning compliance**,
not evidence of an implemented control. No constitutional exception is requested.

<!-- prettier-ignore -->
| Principle or constraint | Before Phase 0 | After Phase 1 | Evidence and implementation gate |
| --- | --- | --- | --- |
| I. Spec before code | Pass | Pass | PO/tech lead planning approval in [spec](spec.md); no open clarification marker; all scope traced below. No code generated. |
| II. Security and identity | Pass | Pass | Distinct Entra issuer policies, server ownership, managed identity/private SQL proposed in contracts and research. Security champion/tech lead identity approval required before implementation. |
| III. Data protection/privacy | Pass | Pass | Synthetic-only, restricted identity/actor mapping, no value-bearing audit or telemetry, US/private/encrypted data boundary. Privacy review and classification/key decision required. |
| IV. Test-first quality | Pass | Pass | External acceptance-derived suites, fail-first evidence, endpoint contracts and >=80% business-logic line coverage required; none claimed run. |
| V. Observability/audit | Pass | Pass | Same-SQL-transaction mutation/audit, separate failed-attempt audit when available, safe correlation; Entra server telemetry only. No unsupported browser ingestion. |
| VI. Simplicity/maintainability | Pass | Pass | One frontend/one existing backend, `/api/v1`, explicit DTOs, SQL transaction; no worker/broker. EF Core justification and simpler SQL alternative in research. |
| VII. Responsible AI | Pass (not applicable) | Pass (not applicable) | No runtime AI feature or member-affecting AI output; do not introduce one. |
| VIII. Documentation current | Pass | Pass for planning artifacts | Plan/model/contracts/validation guide/backlog aligned. T023 requires same-PR delivered docs and architecture reconciliation; no claim that unimplemented setup exists. |
| Constitutional stack/layout | Pass | Pass | Angular/TypeScript, C#/.NET 11, Azure SQL, Bicep-only future IaC; compatible baseline recorded; no hosting implicitly selected. |
| Dependencies | Pass | Pass | No installs. Local npm uses internal proxy; Actions public npm only for reviewed public identities; private/non-npm use approved internal feeds; fail closed. |
| Performance | Pass | Pass (validation gated) | No workload fabricated; PO agreement and basis required before SC-008 measurement. |
| Workflow/tracking | Pass | Pass for design/backlog generation | Milestone and actual T001–T024 issue links recorded; dependencies and read-only analysis required. All tasks remain open. Required CI and one approving PR review remain gates. |

### Approval and sequencing gates

1. **Before identity, persistence, or API implementation:** tech lead and security
   champion review the proposed trust boundaries, DTOs/statuses, concurrency,
   verified-link invariants, atomic audit and same-project conversion. Privacy
   owner reviews identity/actor mapping, submitted text, synthetic fixtures,
   residency and data classification. Record actual reviewers and decisions;
   no names or approvals are fabricated.
2. **Before dependency introduction:** exact version pins, feed access, public/
   private classification, license/vulnerability results, approved Entra and
   SQL/telemetry SDK compatibility, external test discovery.
3. **Before environment-dependent validation:** infrastructure/landing-zone and
   security owners approve hosting, US service/identity data location, private
   SQL/DNS/connectivity, managed identity roles, TLS/encryption and key needs,
   redirect/CORS allowlists, and server Entra telemetry. Provisioning is not
   authorized by this plan; use separate approved Bicep/pipeline work.
4. **Before performance validation:** PO-agreed workload and basis recorded above,
   measurable boundary and warm/cold treatment agreed; no assumed throughput.
5. **Before implementation workflow:** review the generated [linked backlog](tasks.md),
   run `/speckit-analyze`, resolve findings through approved changes and record
   required human approvals. Required build/tests/SAST/secret/dependency checks
   and approving PR review must be real, not inferred from foundation CI.

## Project Structure

### Documentation (this feature)

```text
specs/002-member-profile-demo/
  spec.md                    Existing approved-for-planning requirements; unchanged
  plan.md                    This proposed plan
  research.md                Decisions, alternatives, evidence and clarification assessment
  data-model.md              Proposed records, constraints, transactions
  contracts/
    api-v1.md                Language-neutral public HTTP/DTO/error proposal
    ui-flows.md              Member/staff interaction and accessibility proposal
  quickstart.md              Future validation guide, not current app commands
  tasks.md                   T001–T024, actual open issue links and dependencies
```

The backlog contains exactly 24 proposed tasks, not executed implementation.
T001 records human design/security/privacy/dependency decisions; T002 and T012
also require 003 T012 / issue 30. The dependency table contains same-feature task
IDs only and describes that external prerequisite separately.

### Source code (proposed future layout, not scaffolded)

```text
src/
  frontend/src/app/
    components/              Shared form/error/status presentation where actually reused
    pages/                   Enrollment, own profile/requests, staff maintenance/review
    services/                Auth context and typed API boundary
  backend/                   SAME project introduced by 003
    Api/                     v1 endpoints, issuer policies, errors and correlation
    Models/                  Records, allowed values and concurrency state
    Services/                Ownership, verification, membership/request transitions, SQL/audit
  shared/                    Language-neutral contract only if approved generation/reuse requires it
tests/
  unit/frontend/
  unit/backend/
  integration/backend/
  contract/backend/
infra/                       Future separately approved Bicep only
.github/workflows/           Existing separate application/infrastructure CI
```

**Structure decision**: `src/` and `infra/` are not currently an application.
003 establishes independent frontend/backend builds and external unit tests
before domain work. 002 evolves that same project and adds integration/contract
coverage. Do not copy domain logic into a second runtime or introduce an empty
shared C# library for Angular. Future API contracts may be promoted from these
review artifacts to language-neutral shared definitions only when needed.

## Phase 0: Research outcome

Completed architecture discovery is reused, not repeated. The installed
clarification workflow resolved the explicit feature path and assessed the
spec without changing the spec/checklist: zero questions needed. Functional
rules are sufficient for planning. Workload, production policies, environment
selection and human design reviews are explicit later gates.
See [research](research.md) for the coverage map and decision alternatives.

## Phase 1: Design and validation inputs

The [data model](data-model.md) defines identity links, uniqueness, request
transitions and all-or-nothing write boundaries. The [API contract](contracts/api-v1.md)
specifies routes, allowlist DTOs, nested ownership, safe failures and conditional
writes. The [UI contract](contracts/ui-flows.md) specifies presentation without
relying on client authorization. The [quickstart](quickstart.md) gives future
validation evidence, not working application commands.

### Requirement-to-validation trace

The [backlog](tasks.md) maps every row to tests, implementation and validation.
All tests are written and observed failing before the corresponding code.

<!-- prettier-ignore -->
| Story / requirements | Design responsibilities | Required acceptance evidence |
| --- | --- | --- |
| US1; FR-001–FR-003, FR-005 | Hosted member OTP; current-account enrollment only | Invalid/expired/replayed code denied by provider flow; all five fields required; pending-only own details; no number/email enumeration or claim. SC-001, SC-002. |
| US2; FR-002, FR-004, FR-005 | Workforce staff policy; verify existing/new transaction | Both verification paths; wrong issuer/entitlement denied; concurrent/conflicting links rejected by DB without access grant. SC-001, SC-002. |
| US3; FR-002, FR-007, FR-008 | Own profile and memberships; member contact DTO | Every allowed field; name/DOB/membership overposting denied; identity/sign-in email/link unchanged; nested cross-member access denied; ended history. SC-001, SC-002. |
| US4; FR-014–FR-016 | Transactional audit, privacy-safe errors/server telemetry | Every mutation success/failure; audit insert/commit failure rollback; no profile values in audit or sensitive telemetry/fixtures; TLS/private/residency evidence when environment approved. SC-001, SC-005, SC-006. |
| US5; FR-017 | Semantic forms, focus/error/status presentation including hosted OTP | All member/staff paths keyboard-operable, labels and text errors perceivable with assistive technology; OTP paste; no full-conformance claim. SC-001, SC-007. |
| US6; FR-002, FR-006 | Staff profile CRUD without delete; staff-only protected search | Unique generated member numbers; read/update all five fields; all four search categories; cross-role denial; injection/overposting and empty results. SC-001, SC-002, SC-004. |
| US7; FR-002, FR-009, FR-010 | Staff membership add/edit; member read history | Multiple memberships, unique generated numbers, exact demo labels, required start/optional valid end; no inferred end/status policy; retained ended record. SC-001, SC-002, SC-004, SC-006. |
| US8; FR-002, FR-011–FR-013, FR-016 | Submitted -> Approved/Rejected -> explicit Applied | Submit/approval do not mutate membership; failed handoff visible as simulated; rejected/submitted cannot apply; failed apply preserves approved state and membership; concurrent/repeated apply cannot reapply; cross-member outcomes hidden. SC-001–SC-003, SC-006. |
| NFR-001 | Agreed measurement workload gate | SC-008 measured only after PO agreement; no workload or passing benchmark invented. |

Every listed public route additionally needs contract tests for request/response
shape, allowed methods and fields, token/role/ownership failures, status/error
codes, no-store responses, and conditional-write semantics. Unit tests cover
business branches; SQL integration tests cover constraint races, stale versions,
rollback and failed-attempt audit behavior. No current tooling can demonstrate
these unimplemented application outcomes.

## Phase 2: Backlog and delivery sequencing

The [24-task backlog](tasks.md) preserves all eight stories, explicit fail-first
tests and model constraints, the complete 24-operation contract inventory, one
FR/NFR/SC trace table and actual issue links under milestone 3.
Shared audit/error/telemetry enforcement T011 and semantic UI foundation T013
precede every story mutation/UI implementation despite their later narrative
story headings. Disjoint test tasks may use synthetic fixtures before upstream
stories exist; shared implementation files are serialized.

003 T012 / issue 30 is required before T002 and T012. T001 human review and T020
PO workload agreement can proceed independently of implementation. T009 joins
verified member flows with completed staff membership maintenance; T019 joins
all request prerequisites. T021 requires an approved pre-existing environment
and agreed workload, with no provisioning. T022 supplies executable coverage,
full route contracts and CI/scanners; T023/T024 require validation evidence
before delivered documentation and final acceptance/status synchronization.
No task or issue is complete merely because the backlog was generated.

## Documentation review obligations

Review README status language against the spec's recorded planning-only
approval. Architecture documentation must distinguish deferred queue/central
audit delivery, unsupported direct browser telemetry and unselected hosting
from this scoped demo plan.
T001/T023 own review and same-PR reconciliation of these differences, not
automatic acceptance of those older proposals. This plan adds no
working setup or deployment instructions, so current tooling commands remain
unchanged. Resolve any resulting inaccurate documentation in the same delivery
PR or explicitly record why an unchanged proposal is retained.

## Complexity Tracking

No constitutional violation or exception is proposed. Unresolved design reviews
are gates, not waivers. If a later decision needs an exception, record its
reason and rejected simpler alternative here and obtain the required review;
recording it alone does not approve it.
