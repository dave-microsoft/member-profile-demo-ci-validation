# Research: Initial Application Projects

**Date**: 2026-11-16

**Status**: Proposed technical selections for [003](spec.md), not accepted
architecture or authorization to implement. Audience: contributors and human
design reviewers. This explanation supports the [plan](plan.md); it is not an
additional task backlog.

## Evidence and clarification assessment

The spec records user PO/tech lead approval for planning only. The constitution
v2.3.0, README project layout, repository instructions, documentation guidance,
and existing application CI are the governing evidence.

Read-only `/speckit-clarify` assessment asked and answered **zero** questions.
No critical ambiguities detected worth formal clarification. The spec/checklist
were not edited by this design phase; the existing checklist reads 16/16 checked.
Its checks are requirements-quality assertions, not evidence of working projects.

| Clarification category            | Assessment                                                                                                                                                                          |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Functional scope and behavior     | Clear: contributor project boundaries, not member behavior.                                                                                                                         |
| Domain and data model             | Clear: no domain entities, persistence, identity association, or data scale in this feature.                                                                                        |
| Interaction and UX flow           | Clear: project discovery and file placement; no member workflow.                                                                                                                    |
| Non-functional quality attributes | Clear within scope: independent checks and constitutional controls. API latency/load, operational availability, and business coverage are inapplicable to a non-service foundation. |
| Integration and dependencies      | Deferred to design: compatible versions, testing, dependency feeds, and CI activation below; no external business integration.                                                      |
| Edge cases and failures           | Clear: misplaced source/tests/IaC, unjustified sharing, extra runtimes, and scope expansion are rejected.                                                                           |
| Constraints and tradeoffs         | Clear: constitutional stack and no hosting/deployment choice; implementation methods assessed below.                                                                                |
| Terminology and consistency       | Clear: application projects versus supporting tests/contracts.                                                                                                                      |
| Completion signals                | Clear: FR-001–FR-009 and SC-001–SC-006; no unrun check represented as passed.                                                                                                       |
| Miscellaneous/placeholders        | Clear: no functional clarification markers; excluded demo decisions remain excluded.                                                                                                |

Proceed with a bounded proposed design, not another functional question round.
Deferred implementation choices require review/verification before adoption;
they are not permission to override a spec constraint.

## Decision 1: Compatible Angular planning baseline

**Decision (proposed)**: Angular 22.x with matching Angular CLI 22.x,
Node.js >=24.15.0 <25, TypeScript >=6.0.0 <6.1.0, and RxJS 7.8.x.

**Rationale**: The official compatibility table retrieved on 2026-11-16 lists
Angular 22 with Node `^22.22.3 || ^24.15.0 || ^26.0.0`,
TypeScript `>=6.0.0 <6.1.0`, and RxJS `^6.5.3 || ^7.4.0`.
The proposed intersection is compatible with that published matrix.
The same reference notes Angular and CLI major versions are synchronized
after v9. Choosing one supported Node major avoids an unconstrained
“24.15+” that accidentally permits incompatible future majors.

**Alternatives considered**:

- Angular 21 with its documented TypeScript range: viable, but no existing
  application or migration constraint requires the older baseline.
- Angular 22 on compatible Node 22 or 26: documented alternatives, not selected
  by this proposal. Generic Node 22 in current CI is insufficient to guarantee
  the minimum compatible patch; align to the proposed reviewed 24.x pin.
- Reuse root tooling's Node >=22.17 declaration as the application contract:
  rejected because it permits versions outside Angular 22's matrix.

**Review/verification gate**: Exact Angular/CLI/build, TypeScript, RxJS, Node,
and testing patches must be verified together, pinned, and checked for license
and vulnerabilities before packages are introduced. A compatibility matrix is
not a vulnerability report or proof a package exists on an approved feed.
No new package/version was retrieved or installed during planning.

Official source: [Angular version compatibility](https://angular.dev/reference/versions).

## Decision 2: Host-neutral backend foundation

**Decision (proposed)**: One `Microsoft.NET.Sdk` class-library project targeting
`net11.0` under `src/backend/`, with future `Api/`, `Services/`, and `Models/`
responsibility locations. No sample business class, server, endpoint, database
context, Functions worker, container, or extra application runtime.

**Rationale**: A buildable backend foundation does not require a hosting
decision or exposed behavior. The .NET SDK target-framework documentation
lists `.NET 11` / `net11.0`; its `global.json` guidance requires a full SDK
version rather than a wildcard. This is a backend project, not a running API
or a cross-project shared library. Later approved host work can adapt this
same boundary. This choice intentionally does not promise that arbitrary
future hosting needs will require no project changes.

**Alternatives considered**:

- ASP.NET Core web/minimal API template: supplies a listener and often sample
  endpoints, selecting an unnecessary application-hosting shape now.
- Functions isolated-worker project: binds the initial shape and dependencies
  to a hosting option the spec keeps open.
- Console application or custom no-op host: creates executable behavior without
  a requirement and risks implying an approved service.
- Folder-only backend: simplest on disk but cannot satisfy independent build
  and test outcomes.

**Review/verification gate**: Human review must accept the non-executable
foundation as the intended initial backend shape; otherwise stop and clarify
design rather than silently selecting a host. Choose and verify a full .NET 11
SDK version, compatible language defaults/analyzers, and internal feed access
before implementation. No SDK installation or compilation was attempted.

Official sources: [Target frameworks](https://learn.microsoft.com/en-us/dotnet/standard/frameworks)
and [global.json](https://learn.microsoft.com/en-us/dotnet/core/tools/global-json).

## Decision 3: Default testing tools with real foundation assertions

**Decision (proposed)**: Use Angular CLI's default Vitest/jsdom integration
where compatible with external test placement; use the SDK MSTest template for
backend foundation tests. Reuse Node's native runner for repository/CI-policy
acceptance checks. All test code and test setup stay under `tests/`.

**Rationale and framework justification**: There is no existing application
test framework to preserve. Angular's official testing guide identifies
Vitest/jsdom as defaults, avoiding a separate runner integration and browser
download for basic shell assertions. Microsoft documents `dotnet new mstest`
and `dotnet test` as a supported C# testing route; it is not a constitutional
mandate or proof of .NET 11 package compatibility. MSTest provides independently
executable C# validation without introducing a production framework.

Meaningful initial assertions include:

- Frontend: the neutral root shell renders its agreed semantic heading;
  test discovery includes external tests and excludes source/generated output.
- Backend: evaluated target framework and assembly/project inputs match the
  foundation boundary; forbidden cross-project/package references are absent;
  test files are not compiled into the application assembly.
- Repository: exactly two application boundaries; source/test/IaC separation;
  no speculative shared library, extra runtime, or member/HTTP sample behavior.
- CI: actual required checks execute and failures propagate; missing/zero tests,
  dependency-policy failures, deleted projects, and unavailable feeds do not
  report success.

These are project-foundation tests, not manufactured business logic.
Coverage of nonexistent business logic is N/A, not 100% or an empty passing
suite. The >=80% business-logic requirement and public-endpoint contract tests
apply when approved behavior exists. Do not add fake methods merely to collect
coverage. DOM emulation also does not prove browser/accessibility conformance.

**Alternatives considered**:

- Karma/Jasmine: Angular documents continued support, but choosing the legacy
  runner for a new workspace adds browser management without an existing need.
- Standalone Vitest configuration/custom builders: unnecessary integration
  before proving the default builder's layout support.
- xUnit/NUnit: valid candidates but no existing tests require them; MSTest is
  the proposed SDK-template route, still requiring human review.
- Node checks only: useful for layout and CI but insufficient on their own to
  demonstrate the frontend's rendering/test integration or .NET test setup.
- Always-true template tests or no discovered tests: rejected; they do not
  satisfy FR-007 or truthful CI.

**Review/verification gate**: Angular CLI's documented include paths are
relative to project source root. Its configuration supports test inclusion
and test TypeScript configuration, but documentation alone does not establish
that this repository's external `tests/unit/frontend/` paths work on the pinned
version. Validate discovery, compilation, import resolution, execution, and
exclusion from production output before acceptance. Keep test-only setup and
providers out of `src/`. If unsupported, stop for a reviewed compatible approach;
do not colocate tests or introduce another builder automatically.

Pin and review MSTest/runner/coverage components and .NET 11 compatibility
before retrieval. New test tooling is proposed, not already approved by the
spec. Native-runner checks should not become an alternative planning workflow.

Official sources: [Angular unit testing](https://angular.dev/guide/testing),
[Angular test command](https://angular.dev/cli/test),
[Angular workspace configuration](https://angular.dev/reference/configs/workspace-config),
and [C# with MSTest](https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-csharp-with-mstest).

## Decision 4: Separate dependency trees and fail-closed feeds

**Decision (proposed)**: Retain the root package as repository tooling, add a
separate frontend manifest/portable lock only during authorized implementation,
and explicitly validate that tree before hosted retrieval. Do not introduce npm
workspaces. Configure approved internal non-npm sources before .NET retrieval.

**Rationale**: Existing `package.json` is tooling only.
`.github/scripts/ci-foundation.mjs` rejects manifest workspaces and unknown
identities and checks integrity/portable lock requirements.
`.npmrc` selects the internal npm registry and omits registry-resolved lock URLs.
The future frontend must not bypass these disclosure controls merely because
its packages are in a different directory.

**Alternatives considered**: A single root workspace or merged tooling/application
package would require a wider dependency-policy change and couple independent
validation; public npm locally or public non-npm fallback violates the
constitution. An unvalidated second lock on Actions risks disclosing private
package identities.

**Review/verification gate**: Classify all direct/transitive identities as public
or private through human review before populating a public inventory. Actions
may retrieve only reviewed public npm identities from public npm. Local npm,
private packages, and non-npm packages retain their required internal feeds.
Unknown identity, inaccessible audit service, missing credentials, unacceptable
license/vulnerability results, or feed failure blocks introduction. No approved
NuGet feed URL or runner access was available as verified evidence; none is
invented. Review any necessary package lifecycle script before enabling it.

Repository sources: [Constitution](../../.specify/memory/constitution.md),
[dependency tooling](../../docs/development-tooling.md),
[CI helper](../../.github/scripts/ci-foundation.mjs), and
[public tooling inventory](../../.github/ci/public-npm-packages.json).

## Decision 5: Activate CI with projects, not after them

**Decision (proposed)**: Extend the existing application gate in the same future
change as project introduction. Provide executable independent build/tests,
truthful result aggregation, and regression tests; preserve separate
infrastructure validation and no deployment.

**Rationale**: `readiness()` currently returns `implemented: false`; affected
`src/` or non-tooling test files fail. The current workflow uses generic Node 22
and does not install a .NET SDK. These are intentional foundation limits, not
already-working application checks. New projects alone would leave CI red.

**Alternatives considered**: Set an unconditional implemented flag, skip missing
checks, loosen path selection, or suppress the readiness failure: rejected as
false success. Add a separate all-new CI platform: unnecessary scope. Activate
deployment after builds: expressly excluded.

**Review/verification gate**: Preserve stable result names and all-event workflow
activation; account for deletions/renames, shared/config/test changes, manual
runs, and fail-closed revision comparison. Verify nonzero test discovery and
failure propagation. Unknown/missing/blocked tools or scans must never become
“passed.” Keep read-only permissions and no cloud credentials. Add approved
language-specific validation when those files exist; review new linter packages
under the same dependency rules. Required SAST and secret scanning remain
unimplemented until actual approved executable checks supply evidence.

Repository sources: [Application workflow](../../.github/workflows/application-ci.yml),
[infrastructure workflow](../../.github/workflows/infrastructure-ci.yml),
[CI helper](../../.github/scripts/ci-foundation.mjs), and
[tooling guidance](../../docs/development-tooling.md).

## Decision 6: No domain model, shared contract, or infrastructure artifact

**Decision**: `data-model.md` and `contracts/` are inapplicable to this
contributor-only foundation and are not created. Do not create `src/shared/`,
integration/contract suites, or `infra/` without an approved concrete use.

**Rationale**: FR-005 requires real approved reuse; FR-006 and FR-009 prohibit
scope creep. There are no domain entities, persistence, external interface, or
public endpoints in 003. Internal build/test entrypoints belong in the
quickstart. Member models, API contracts, authentication/authorization,
telemetry/audit delivery, and hosting decisions belong to separately reviewed
feature work.

**Alternatives considered**: Empty API schemas, speculative shared DTOs, an
invented business model, or a runnable sample endpoint merely to exercise
contract tests would manufacture requirements.

The [architecture overview](../../docs/architecture/member-profile-overview.md),
[design review](../../docs/architecture/design-review.md), and
[infrastructure proposal](../../docs/architecture/azure-infrastructure.md)
were read as proposed context. Even where their tables call a direction
“selected,” their review status is proposed. They neither approve hosting for
003 nor authorize a third runtime/audit worker. No accepted ADR was present in
`docs/adr/` at inventory time. The separate architect owns the demo review.

## Source verification and limits

Official external pages cited for Angular compatibility/testing/configuration,
.NET target frameworks/SDK pinning, and MSTest were fetched successfully on
2026-11-16. Only public documentation URLs were requested; no repository source,
member data, credentials, or private package identities were submitted.
These sources support the claims above but are mutable and do not replace
review of exact pinned releases.

No application scaffolding, package download/install, compilation, test runner
compatibility experiment, license/vulnerability scan, hosted CI run, browser
test, deployment, or performance measurement occurred. Source retrieval is not
implementation validation. Exact version approval, external Angular test
discovery, internal non-npm feed access, and human design/security reviews remain
explicit gates. Milestone and task issue links are recorded in the companion
plan and task backlog; no implementation completion is implied.
