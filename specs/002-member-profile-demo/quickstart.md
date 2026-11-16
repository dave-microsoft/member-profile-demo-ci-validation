# Quickstart: Future Demo Validation

Use this future how-to guide to assess the proposed demo against the
[specification](spec.md). The user as product owner and tech lead approved the
spec **for planning only on 2026-11-16**. No application exists; design acceptance,
implementation, installation and deployment are not authorized by that approval.

## Prerequisites

- Review the [plan and gates](plan.md), [data model](data-model.md),
  [API contract](contracts/api-v1.md), and [UI flows](contracts/ui-flows.md).
- Target Angular/CLI 22, Node.js >=24.15.0 <25, TypeScript 6.0.x, RxJS 7.8.x,
  and .NET 11 with reviewed exact pins.
- First establish the reviewed 003 foundation. Convert its **same** host-neutral
  backend project to an ASP.NET Core API only after 002 design approval; do not
  create a second runtime.
- Obtain tech lead, security champion and privacy owner review before identity,
  persistence or API implementation. Review all new dependencies, including
  proposed Vitest/jsdom, MSTest and SQL/identity/telemetry packages, for source,
  compatibility, license and vulnerabilities before introduction.
- Local npm uses only `https://packagefeedproxy.microsoft.io/npm/`.
  Actions uses public npm only for reviewed public package identities.
  Private/non-npm packages use approved internal feeds everywhere; the approved
  non-npm feed/access is not supplied here. Feed failure blocks retrieval, never
  permits fallback. No install is part of this guide's current execution.

## Setup and command availability

There are no verified application setup, migration, seed, run, browser or test
commands yet. Do not treat illustrative `dotnet`/Angular commands as working
entrypoints or perform scaffolding to make this guide executable.

During authorized implementation, record actual manifest/project/SDK paths and
independently runnable frontend/backend commands here, along with approved
identity redirect configuration, private SQL access, synthetic seed/reset
procedure and telemetry verification. Do not put credentials, connection strings
or identifying fixtures in these instructions.

Current repository root commands are:

```powershell
npm run lint
npm run test:tooling
```

They validate documentation/format/links and tooling only, not member behavior.
No application acceptance result follows from a passing tooling check.

## Future fail-first validation sequence

1. Derive tests from every scenario in [spec](spec.md) and each of the 24 proposed
   public route/method combinations in the [contract](contracts/api-v1.md).
   Write tests first and retain evidence of expected failure before implementation.
2. Keep suites external to source:
   `tests/unit/frontend/`, `tests/unit/backend/`,
   `tests/integration/backend/`, `tests/contract/backend/`.
   Review nonzero test discovery and failure propagation for both stacks.
3. Use synthetic, non-identifying test records and opaque actor identifiers.
   Never paste member data or account information into AI prompts or test reports.
   Identity-flow validation uses approved test-tenant accounts without copying
   personal account information, codes or tokens into fixtures/logs.
4. Run independent frontend/backend builds and unit tests using their future
   reviewed entrypoints. Measure at least **80% business-logic line coverage**.
   A source-file ratio or unrun test suite is not coverage evidence.
5. Run SQL-provider integration and endpoint contract suites. Include token trust/
   scope/entitlement, nested ownership, unknown fields, safe errors/no-store,
   missing/stale/valid If-Match, unique constraints and transaction rollback.
6. Exercise browser and assistive-technology journeys below. Record tooling and
   unverified behavior; do not infer accessibility from a unit test or static scan.
7. Only after separately approved environment readiness, verify private SQL,
   managed identities, region/encryption and compliant server telemetry.
8. Only after PO workload agreement and basis are recorded in [plan](plan.md),
   measure NFR-001 / SC-008. Never invent throughput or benchmark results.

## Planned acceptance scenarios

Exercise every story with synthetic data (**SC-001**).

<!-- prettier-ignore -->
| Scenario | Expected evidence |
| --- | --- |
| US1 — Enrollment | Require all five fields. Hosted invalid/expired/reused OTP is denied. Pending applicants see only their own submission; no profile access/edit or lookup-based ownership. |
| US2 — Verification | Entitled workforce staff record simulated verification for both an existing profile and new profile creation. Wrong issuer/entitlement and concurrent/conflicting links fail without access grant. |
| US3 — Member self-service | Own profile and membership history visible. Only contact email, phone and mailing address editable; name/DOB/membership overposting denied; sign-in identity/email/link unchanged. |
| US4 — Privacy and audit | Every successful mutation and audit commit together. Audit insert/commit failure rolls back; failed attempts audited when available. No profile values in audit; no personal/account data, payloads, codes or tokens in telemetry; safe correlation not stack traces. |
| US5 — Accessibility | All required controls keyboard-operable with visible focus, labels/instructions and text errors available visually and to assistive technology; OTP paste works. |
| US6 — Staff maintenance | Staff create/read/update profiles and search by all four supported identifiers; unique member numbers; empty results and unauthorized access handled safely. |
| US7 — Membership history | Multiple memberships with unique membership numbers, exact demo labels, required start and optional valid end. Reject missing start/end-before-start; retain ended history. Do not require an end date just because status is ended. |
| US8 — Requests | Submit creates Submitted without membership change. Simulated handoff failure visible, not external approval. Staff approval/rejection recorded separately; approval alone does not apply. Explicit apply commits membership/request/audit together; failed/stale/concurrent/repeated apply cannot double-apply or misreport Applied. |

### Success-criterion evidence checklist

- **SC-002:** Test every pending, cross-member and nonstaff scenario on server
  reads and writes, including nested inconsistent IDs. No unauthorized detail,
  existence confirmation or mutation.
- **SC-003:** No Submitted/Rejected request may update membership; every Applied
  follows recorded approval. Inject failures between membership, request and
  audit writes; verify Approved and unchanged membership on rollback.
- **SC-004:** Race generated numbers and verified links, reject invalid dates,
  and retain multiple/ended membership numbers and dates.
- **SC-005:** Inspect audit/events/telemetry and test artifacts for privacy.
  Verify all state changes have atomic internal audit. Opaque actor mappings
  remain restricted, not exposed in telemetry.
- **SC-006:** Review visible synthetic/demo/simulated labels, original handoff
  failures and open production decisions. No claim of production readiness.
- **SC-007:** Complete all specified member/staff flows by keyboard and assistive
  technology, including errors/conflicts and hosted OTP paste. Record limitations;
  do not claim full accessibility certification.
- **SC-008 / NFR-001:** Record the PO-agreed workload **and basis**, journey mix,
  measurement boundary and cold/resume treatment in the plan before validation.
  Then verify p95 API latency under 300 ms. Load is currently unknown; no passing
  performance result is asserted.

## Environment gates and limitations

Hosting is unselected; Container Apps is a candidate only. Infrastructure owners
and security reviewers must approve US service/identity data location, private
SQL/DNS access, managed identity roles, TLS/encryption and key needs, redirect/
CORS allowlists, and Entra-authenticated Application Insights with local auth
disabled before environment-dependent tests. This is not permission to provision.

Member hosted External ID email OTP and separate workforce staff issuer policies
remain Proposed. Do not bypass identity for local validation. The Application
Insights JavaScript SDK does not support Entra ingestion; telemetry is server-only
with reviewed collector suppression/redaction, no browser/local-auth workaround.

No queue, worker or central audit integration is required for the demo. Real
member data, production identity proofing, real review integration, production
membership rules and production deployment remain excluded. Production retention,
minor-member handling, availability and recovery policies remain human decisions.

## Backlog and evidence references

The [implementation backlog](tasks.md) contains exactly T001–T024 with actual
open issue links under milestone 3. T001 records design/security/privacy/
dependency and exact bounds/schema decisions before implementation. T002/T012
also require the completed 003 T012 foundation ([issue 30](https://github.com/dmd0822/member-profile-demo/issues/30)).
T011/T013 provide early shared audit and semantic UI controls before story
mutation/UI delivery. Test authors can use isolated synthetic fixtures rather
than wait for every upstream route; no missing route counts as a passing test.

T020 records actual PO workload input; T021 validates only in an explicitly
approved pre-existing environment without provisioning. T022 verifies >=80%
business-logic line coverage, every public route's contract and executable CI/
scanners; T023 updates delivered documentation and T024 proves all stories and
synchronizes completion evidence.

No app builds, acceptance/contract/SQL/browser tests, dependency scans or
performance checks have run in this planning phase. Read-only
`/speckit-analyze` follows backlog generation. This guide and the linked issues
authorize neither implementation nor deployment; all review gates remain.
