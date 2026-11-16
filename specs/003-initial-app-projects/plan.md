# Implementation Plan: Initial Application Projects

**Branch**: `dave-microsoft-spec-project-planning` (assigned checkout) | **Date**: 2026-11-16 | **Spec**: [Initial Application Projects](spec.md)

**Input**: Feature specification from `specs/003-initial-app-projects/spec.md`.
Explicit Spec Kit target: `003-initial-app-projects`; setup reports the feature
identifier as its branch, not the physical checkout branch above.

**GitHub Milestone**: [003 Initial application projects](https://github.com/dmd0822/member-profile-demo/milestone/2).
The milestone links the spec, plan, and [task backlog](tasks.md). Each task has a
corresponding issue; implementation completion remains outstanding.

**Status**: Proposed design artifacts, not implementation-ready. The user acting
as product owner and tech lead approved the spec for planning on 2026-11-16.
That approval does not approve the design, new test tooling, dependencies,
hosting, implementation, or deployment. The linked backlog records task
tracking. No application files or packages are introduced by this plan.

## Summary

Establish one Angular/TypeScript frontend project and one C#/.NET 11 backend
foundation with independent builds and meaningful foundation tests. Preserve
the repository's source, test, infrastructure, and documentation boundaries.
Activate applicable application CI in the same future implementation change;
the existing CI deliberately rejects domain files without executable validation.

Recommend Angular/CLI 22, Node.js 24.15.0 or later **24.x**, TypeScript 6.0.x,
and RxJS 7.8.x as the compatible planning baseline. Recommend a host-neutral
.NET 11 class-library project as the backend foundation, not a running API.
These are bounded proposals requiring human design review. No Functions,
Container Apps, frontend hosting, business endpoint, identity flow, database
access, shared library, audit worker, infrastructure, or deployment is selected.
The proposed demo architecture remains separate and unapproved.

See [research](research.md) for evidence, alternatives, and review gates, and
the [quickstart](quickstart.md) for planned future validation entrypoints.

## Technical Context

**Language/Version**: Angular 22.x / matching CLI 22.x; TypeScript 6.0.x;
Node.js >=24.15.0 <25; RxJS 7.8.x. Backend C# with the approved .NET 11 SDK's
compatible default language version and `net11.0`, not a floating `latest`.
Exact package patches and full SDK version remain package-introduction gates,
not claims of tested pins. Record them in manifests, portable locks, and
`global.json` before any authorized implementation retrieval.

**Primary Dependencies**: Angular and its matching CLI/build tooling. Proposed
Angular-default Vitest/jsdom; proposed .NET SDK MSTest template tooling.
No new production backend package, ORM, identity SDK, HTTP server framework,
cloud SDK, state library, or component library is needed by this foundation.
Every introduced direct/transitive dependency requires license, vulnerability,
source, and compatibility review before introduction.

**Storage**: N/A; no persistence or data access. Azure SQL remains the
constitutional constraint for later approved persistence work.

**Testing**: Existing Node native test runner for repository foundation and
CI-policy acceptance tests; proposed Angular CLI unit-test builder with
Vitest/jsdom for a neutral root-shell assertion; proposed MSTest project for
backend project/assembly-boundary assertions. All test files and test setup
remain under `tests/`. The pinned CLI must demonstrate discovery and execution
outside `src/` before this design is accepted for implementation. No empty,
always-true, or invented business tests; zero discovered required tests fail.

**Target Platform**: Browser frontend; host-neutral .NET backend assembly.
Azure hosting remains undecided. The backend has no listener, route, deployment
image, Functions binding, or operational service in this feature.

**Project Type**: One frontend application project and one backend foundation
project; a backend test project is supporting validation, not another runtime.
Later approved hosting work adapts the same backend boundary rather than
silently adding a third application project.

**Performance Goals**: N/A for this foundation: no API behavior or agreed peak
load. The constitutional p95 API target below 300 ms remains binding when an
API and expected peak load are agreed; no benchmark or throughput is invented.

**Constraints**: Tests outside `src/`; no speculative sharing or domain
behavior; independent build/test without cloud access; fail-closed dependency
policy; truthful CI; same-PR documentation accuracy; deployment disabled.
Local npm uses the approved internal registry. GitHub Actions uses public npm
only for reviewed public identities. Non-npm/private dependencies use approved
internal feeds everywhere; missing access blocks retrieval without fallback.

**Scale/Scope**: US1 and US2 only; FR-001–FR-009 and SC-001–SC-006. No member
count, availability target, recovery target, regional deployment, or load
assumption is needed to classify and validate project boundaries.

## Constitution Check

Pre-research evaluation and post-design recheck use constitution v2.3.0.
“Pass” below means the bounded planning design respects the principle, not
that executable implementation checks or pending human reviews have passed.

| Principle                            | Before Phase 0        | After Phase 1         | Evidence and implementation gate                                                                                                                                                                                                                                                                                                      |
| ------------------------------------ | --------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| I. Spec Before Code                  | Pass                  | Pass                  | Spec records PO/tech lead planning approval; no open functional clarification markers. Design remains proposed. Parent must produce tasks and run analysis; explicit implementation authorization is still required.                                                                                                                  |
| II. Security and Identity by Default | Pass                  | Pass                  | No service, identity flow, credential, or resource created. Later services retain Entra ID, managed identity, Key Vault for unavoidable secrets, private data access, and resource-scoped least privilege. Do not expose an unauthenticated sample API. Security review applies before affected behavior is added.                    |
| III. Data Protection and Privacy     | Pass                  | Pass                  | No member data, fixtures containing PII, persistence, logs, or external payloads. Future data classification, encryption at rest/TLS 1.2+, and approved US residency remain mandatory; no production readiness claimed.                                                                                                               |
| IV. Test-First Quality               | Pass                  | Pass                  | Foundation acceptance tests fail before scaffolding/CI changes. Real shell/configuration assertions replace template placeholders. Business-logic coverage is N/A, not 100%; later business logic requires >=80% line coverage and every public endpoint requires contract tests.                                                     |
| V. Observability and Auditability    | Pass                  | Pass                  | No state-changing business operation or running service exists. Do not add a telemetry resource/SDK merely for scaffolding. Application Insights structured logs/traces/metrics with Entra ingestion/local auth disabled, safe correlation IDs, and who/what/when/outcome audits remain prerequisites for later operational behavior. |
| VI. Simplicity and Maintainability   | Pass                  | Pass                  | Exactly two application boundaries; no shared library, extra worker, service framework, or hosting selection. Proposed test-framework justification and alternatives are written here and in research; human review and dependency checks precede adoption. No public interface exists to version yet.                                |
| VII. Responsible AI                  | Pass (not applicable) | Pass (not applicable) | The application foundation uses no AI. AI-assisted authorship is not a member-facing AI capability. Future applicable AI use still requires review/fallback, versioned prompts/model/evaluations, enabled safety filters, and permission-aware grounding.                                                                             |
| VIII. Documentation Stays Current    | Pass                  | Pass                  | README and development-tooling guidance were reviewed; README links the current feature backlog. Future project/CI implementation must update affected setup, structure, test, and configuration documentation in the same PR, not present these proposed commands as existing.                                                       |

Additional constraints: constitutional stack preserved; Bicep-only future IaC;
no dependency introduced without checks; no package feed fallback; no deployment.
GitHub milestone and task issue links are recorded in the feature artifacts.
Required CI and at least one approving PR review remain mandatory; no self-approval.

### Planning and implementation gates

1. **Planning authority satisfied**: target spec is approved for planning.
   Functional clarification assessment asked zero questions; missing hosting
   and demo identity choices are explicitly excluded, not hidden ambiguities.
2. **Design review pending**: humans must review the host-neutral backend shape,
   Angular toolchain, test-framework justifications, external test discovery,
   and CI/dependency design. No accepted ADR exists for these selections.
   The separate architect's demo review is not duplicated or presumed complete.
3. **Package/toolchain introduction blocked pending checks**: choose exact
   patches/full SDK; verify compatibility, approved internal non-npm feed
   identity/access, and license/vulnerability results before retrieving or
   introducing packages. Feed unavailability must stop the affected work.
4. **Backlog gate**: review the generated [tasks](tasks.md), synchronized
   milestone/issues, and consistency analysis before implementation. Neither
   planning approval nor task generation authorizes implementation.
5. **Implementation and PR gates remain**: explicit later authorization,
   observed fail-first tests, applicable executable CI and security scans,
   same-PR documentation, and required human review. No deployment route.

The design is sufficiently bounded to document as a proposal; it is not
reported as fully reviewed or ready to scaffold. Failure of an applicable
gate stops that activity rather than being recorded as a passing check.

## Project Structure

### Documentation (this feature)

```text
specs/003-initial-app-projects/
  spec.md                    # Existing; approval writer owns changes
  checklists/requirements.md # Existing; approval writer owns changes
  plan.md                    # This proposed design
  research.md                # Phase 0 decisions, sources, alternatives
  quickstart.md              # Phase 1 future validation guide
```

No domain entities, persistence model, public API, or cross-stack contract is
introduced; `data-model.md` and `contracts/` are not applicable and are not
created. Contributor build/test entrypoints are documented in the quickstart,
not promoted into fabricated API contracts. The companion `tasks.md` is the
separate task-generation workflow output, not a Phase 1 design artifact.

### Source Code (repository root)

The following is a **future proposed layout**, not existing source:

```text
src/
  frontend/                  # One Angular workspace/application
    package.json             # Application dependencies separate from root tooling
    package-lock.json        # Reviewed portable exact dependency lock
    angular.json
    src/app/
      components/            # Reusable UI responsibility
      pages/                 # Routed-view responsibility; no member page yet
      services/              # Frontend-service responsibility; no API client yet
  backend/                   # One host-neutral net11.0 foundation
    MemberProfile.Backend.csproj
    Api/                     # Future HTTP-handling responsibility, no endpoint
    Services/                # Future business-service responsibility, no logic
    Models/                  # Future backend-model responsibility, no member model
tests/
  unit/
    foundation/              # Executable layout and project-boundary assertions
    frontend/                # Root-shell tests and test-only setup/configuration
    backend/
      MemberProfile.Backend.Tests.csproj
.github/
  scripts/ci-foundation.mjs  # Extend existing selection/readiness/execution
  workflows/
    application-ci.yml       # Activate actual Angular/.NET checks
    infrastructure-ci.yml    # Preserve separate infrastructure gate
```

**Structure Decision**: Follow README/FR-002/FR-003 responsibility locations.
Record empty responsibility areas without creating dummy business classes or
routes solely to fill folders. Keep root npm tooling independent; use a
separate frontend manifest/lock rather than introducing npm workspaces.
Do not create `src/shared/` without an approved reuse requirement, or `infra/`,
`tests/integration/`, or `tests/contract/` without actual corresponding work.
The latter locations remain reserved by FR-004–FR-006, not deleted conventions.
Do not colocate Angular-generated tests under `src/`; configure discovery and
TypeScript test inclusion for `tests/unit/frontend/`, validating relative
paths against the pinned builder. A failure is a design blocker, not permission
to weaken the layout or silently select another framework.

## Phase 0: Research Outcome

[Research](research.md) resolves implementation-method unknowns into bounded
proposals: compatible frontend versions, host-neutral backend, default test
tooling with explicit review, separate dependency trees, fail-closed CI
activation, and absence of domain contracts/data models. Exact patch approval,
feed access, executable compatibility, and human design reviews remain
explicit pre-introduction gates, not unresolved functional requirements.
No application dependency was retrieved to conduct this research.

## Phase 1: Design and Validation

### Independent project validation

- Frontend build uses only the frontend workspace. Tests assert a neutral,
  semantic root shell and test discovery outside `src/`; there are no member
  pages, profile forms, identity controls, or backend calls. Do not retain
  framework-template branding tests as evidence of business coverage.
- Backend build targets the single `net11.0` foundation. Its external MSTest
  project asserts meaningful foundation constraints such as evaluated target
  framework, forbidden project/dependency references, and separation of
  application and test inputs. No HTTP host or fake service method is added to
  make tests exist.
- Native Node acceptance checks cover both project roots, required responsibility
  locations, no extra runtime projects, no direct frontend/backend implementation
  references, no speculative sharing, test categories, and source/IaC isolation.
  Keep all checks outside `src/`.
- Planned scripts and explicit-path .NET commands are in the quickstart. Each
  must propagate build/test/restore failures. A missing project/tool, empty test
  discovery, unsupported configuration, or inaccessible feed is failure/blocked,
  never a passing “available tests” check.

### Application CI activation

Current evidence: `.github/scripts/ci-foundation.mjs` returns `implemented: false`
from `readiness()` and rejects affected domain files. Application CI currently
runs repository tooling only. Adding project files without changing this gate
will fail as designed.

The future application change must provide frontend and backend build/test
execution and update readiness/reporting in the same PR. Do not set an
unconditional readiness flag or remove domain checks. Preserve the stable
**Application CI result** and **Infrastructure CI result** names, read-only
permissions, pinned Actions, no persisted checkout credentials, and no
workflow-level path filters. Result aggregation must run and report failures,
not mask failed or skipped required children.

Select dependent checks for source, shared assets if later present, matching
tests, package/lock/build configuration, SDK pins, and CI changes. Retain
fail-closed revision comparison, deletions/renames, manual dispatch validation,
and explicit not-applicable reporting for unrelated changes. Unchanged
infrastructure stays separate and no Bicep deployment is activated.

Upgrade application CI's Node selection to an exact reviewed compatible 24.x
patch and add a full reviewed .NET 11 SDK pin before execution; do not rely on
the current generic Node 22 selection satisfying Angular 22.

Before any frontend or root npm retrieval/audit on Actions, validate the
corresponding manifest and lock against reviewed public identities and allowed
sources. The root validator currently rejects npm workspaces; extend it to
validate the frontend's separate tree explicitly with regression tests. Never
send unknown/private identities to public npm to find out whether they exist.
Keep integrity checks, portable locks without registry-resolved URLs, explicit
environment registries, and the no-fallback policy. Do not blindly enable
lifecycle scripts; review any required build-tool install behavior first.

MSTest/NuGet and other non-npm dependencies require approved internal feeds on
local machines and Actions. No internal NuGet source/access configuration was
verified; do not invent one, use a public fallback, or expose secrets to
untrusted pull requests. Missing feed capability blocks the backend check.
Required SAST, secret scanning, dependency checks, and human reviews must be
implemented/verified or reported blocked; existing tooling is not proof they
have passed. No write permission, cloud login, image push, CD, or resource
change belongs in this feature.

### Acceptance and success-criteria traceability

Write and observe acceptance tests fail before the corresponding scaffold or
CI implementation. Assert actual files/results, not only documentation text.

| Story / requirements | Success criteria | Planned acceptance evidence                                                                                                                                                                                                                                                                           |
| -------------------- | ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| US1; FR-001–FR-003   | SC-001           | Identify exactly two application project roots; classify every application source file; check separate frontend components/pages/services and backend Api/Services/Models responsibility locations. Negative case: extra runtime or misplaced source is rejected.                                     |
| US2; FR-004          | SC-002           | Enumerate tests and test-only setup; none under `src/`; unit/integration/contract tests use corresponding categories. Deliberately misplaced fixtures fail validation. Do not create empty integration/contract suites.                                                                               |
| US2; FR-005          | SC-004           | No shared library without approved reuse; no frontend reference to backend implementation. Negative fixtures cover direct project/package/source references and a speculative shared library.                                                                                                         |
| US1/US2; FR-006      | SC-005           | Source/IaC boundary checks reject runtime files in `infra/` and Bicep under application roots; no resources or deployment introduced.                                                                                                                                                                 |
| US1/US2; FR-007      | SC-003           | Independent frontend build and nonzero discovered shell tests; independent backend build and nonzero discovered foundation tests. CI scenario tests exercise selected checks, deletion/config/test changes, failed commands, unavailable tools/feeds, zero tests, and truthful not-applicable states. |
| US1/US2; FR-008      | SC-006           | Contributor review follows delivered setup/build/test guidance from a clean authorized environment; exact entrypoints and outcomes match implementation. Planned commands are not represented as current until verified.                                                                              |
| US1/US2; FR-009      | SC-006           | Review project/dependency inventory and shell to confirm zero enrollment/profile/membership/identity/database behavior, extra runtimes, hosting selections, or deployment activation.                                                                                                                 |

Security/privacy negative cases here concern package disclosure, credentials,
boundaries, and unintended endpoints, not invented login functionality.
Unauthorized/cross-member and API failure/contract tests belong to 002 once its
design is reviewed. Minimal shell semantics need applicable accessibility review;
DOM emulation does not prove browser or screen-reader conformance. No API latency,
business-logic coverage, or production-readiness result is claimed.

### Dependencies and sequencing

Design/test-tooling review and exact-version/feed checks precede package
introduction. Fail-first structural/shell/CI-policy tests precede the matching
implementation; project source and CI activation must land together rather
than knowingly leaving a broken domain gate. Independent frontend/backend
validation can be separate checks but must aggregate truthfully. Documentation
updates and negative-path evidence precede acceptance.

002 must reuse this foundation rather than scaffold duplicate projects.
Its identity, domain model, hosting, public API, audit topology, and expected
peak-load decisions do not become approved through 003. A need to select one
of those designs requires the appropriate separate review/scope, not expansion
of this foundation.

## Complexity Tracking

No constitutional exception is requested. Proposed test tooling is justified
under principle VI, not exempted from human review or dependency policy.
Absent business APIs/data/AI make their behavior-specific checks inapplicable,
not waived. Remaining implementation gates are explicitly incomplete work,
not approved exceptions.

| Violation     | Why Needed | Simpler Alternative Rejected Because |
| ------------- | ---------- | ------------------------------------ |
| None proposed | N/A        | N/A                                  |
