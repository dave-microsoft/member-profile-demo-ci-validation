# Tasks: Initial Application Projects

**Input**: [Spec](spec.md), [plan](plan.md), [research](research.md), and
[planned validation guide](quickstart.md).

**GitHub Milestone**: [003 Initial application projects](https://github.com/dmd0822/member-profile-demo/milestone/2).

**Status**: Planning backlog only. All tasks are open. Spec approval permits
planning, not scaffolding, package introduction, or deployment. T001 records
the remaining human design and dependency gates.

Tests are mandatory under the constitution, overriding optional-test template
boilerplate. Observe acceptance assertions fail before implementing their
behavior; a missing tool is a prerequisite blocker, not a failing assertion.
Every implementation task includes review/update of affected documentation in
the same PR. No business logic or public endpoint is introduced here.

## Phase 1: Setup and review

- [ ] T001 Review proposed project/test tooling and record exact compatible dependency/SDK pins, license/vulnerability results, approved internal non-npm feeds, and human decisions in `specs/003-initial-app-projects/research.md` and `plan.md` before retrieval or scaffolding (FR-001, FR-007). [Issue](https://github.com/dmd0822/member-profile-demo/issues/23)

## Phase 2: Foundational acceptance boundary

T001 is the blocking design/package gate. Layout acceptance checks below use
Node's existing native runner; no shared library, persistence model, public
API contract, infrastructure, or extra application runtime is needed.

## Phase 3: US1 - Find the initial projects (P1)

**Goal**: Exactly one frontend and one backend, with clear source ownership.

**Independent test**: Fail-first layout assertions reject extra runtimes,
misplaced source/IaC, and member behavior; each project builds independently.

- [ ] T002 [US1] Write failing boundary checks in `tests/unit/foundation/project-boundaries.test.mjs` and expose the proposed root `package.json` test entrypoint; assert project counts, required source responsibilities, no runtime source under `infra/`, no IaC under `src/`, and no member behavior (FR-001–FR-003, FR-006, FR-009). [Issue](https://github.com/dmd0822/member-profile-demo/issues/21)
- [ ] T003 [P] [US1] Establish the neutral Angular shell and components/pages/services ownership in `src/frontend/src/app/`, with reviewed `src/frontend/package.json`, portable lockfile and `angular.json`; no colocated tests, SSR, identity, or domain screens (FR-001, FR-002, FR-007, FR-009). [Issue](https://github.com/dmd0822/member-profile-demo/issues/22)
- [ ] T004 [P] [US1] Establish `src/backend/MemberProfile.Backend.csproj` as the reviewed host-neutral `net11.0` library with Api/Services/Models ownership, exact `global.json` SDK pin and approved internal NuGet source configuration; no sample classes, listener, endpoints, or database (FR-001, FR-003, FR-007, FR-009). [Issue](https://github.com/dmd0822/member-profile-demo/issues/24)

T003 and T004 may proceed in parallel only after T002 fails as intended and
T001 gates pass. Keep shared root/toolchain edits assigned to one owner.

## Phase 4: US2 - External tests and justified sharing (P2)

**Goal**: Independent nonempty tests outside source; no frontend/C# coupling.

**Independent test**: Run each project without the other application or cloud,
reject misplaced tests and forbidden references, and demonstrate deliberate
test failures propagate. Integration/contract locations remain reserved; do not
create fake suites for nonexistent behavior.

- [ ] T005 [US2] Write failing external-test and sharing checks in `tests/unit/foundation/test-boundaries.test.mjs`; cover tests under `src/`, incorrect test categories, speculative shared libraries, and frontend references to backend implementation using positive and negative controls (FR-004, FR-005, FR-007). [Issue](https://github.com/dmd0822/member-profile-demo/issues/28)
- [ ] T006 [P] [US2] Configure approved external Angular test discovery in `src/frontend/angular.json` and `tests/unit/frontend/`; write `app-shell.spec.ts` before its final shell behavior, verify nonzero execution and deliberate failure, and exclude test setup from production output (FR-004, FR-007). [Issue](https://github.com/dmd0822/member-profile-demo/issues/27)
- [ ] T007 [P] [US2] Configure `tests/unit/backend/MemberProfile.Backend.Tests.csproj` and `FoundationBoundaryTests.cs`; verify target framework/reference/source boundaries, fail first before correcting test configuration, and demonstrate independent nonempty test execution without dummy business methods (FR-004, FR-005, FR-007). [Issue](https://github.com/dmd0822/member-profile-demo/issues/26)

T006/T007 can run in parallel after their respective project and T005 exist.
External discovery incompatibility stops work for review; do not relocate tests.
Business-logic coverage is N/A, not 100%.

## Phase 5: CI, documentation, and acceptance

- [ ] T008 Write failing activation regressions in `tests/unit/tooling/ci-foundation.test.mjs` for real Angular/.NET checks, missing/deleted projects, zero tests, failed checks, rename/deletion/config/shared selection, stable results, and nested dependency validation (FR-007). [Issue](https://github.com/dmd0822/member-profile-demo/issues/25)
- [ ] T009 Activate independent builds/tests in `.github/workflows/application-ci.yml` and `.github/scripts/ci-foundation.mjs`, including reviewed nested frontend package inventory before public requests; preserve stable results, separate infra CI, read-only permissions and no deployment (FR-007). [Issue](https://github.com/dmd0822/member-profile-demo/issues/31)
- [ ] T010 Establish approved executable language lint, SAST, secret and dependency checks in `.github/workflows/application-ci.yml` and supporting configuration; fail explicitly on findings or unavailable scanners and preserve private/non-npm internal feeds (FR-007; constitutional CI gates). [Issue](https://github.com/dmd0822/member-profile-demo/issues/32)
- [ ] T011 Update `README.md`, `docs/development-tooling.md`, and `specs/003-initial-app-projects/quickstart.md` with delivered boundaries and actual independent commands, explicit feed requirements, and honest coverage/hosting limits (FR-008, FR-009). [Issue](https://github.com/dmd0822/member-profile-demo/issues/29)
- [ ] T012 Execute all acceptance scenarios in `specs/003-initial-app-projects/quickstart.md`, record results/blocked checks, run scoped convergence, and synchronize `tasks.md` and issue states only with actual completion evidence (FR-001–FR-009). [Issue](https://github.com/dmd0822/member-profile-demo/issues/30)

Deliver T003/T004 and T009 in the same implementation PR: current CI deliberately
blocks domain source until real validation exists. Do not merge an intermediate
project-only change that knowingly leaves the required gate broken.

## Dependencies and execution order

| Task | Prerequisites                                                     |
| ---- | ----------------------------------------------------------------- |
| T001 | None; human reviews and approved feed access required to complete |
| T002 | T001                                                              |
| T003 | T001, T002                                                        |
| T004 | T001, T002                                                        |
| T005 | T001, T002                                                        |
| T006 | T003, T005                                                        |
| T007 | T004, T005                                                        |
| T008 | T001, T002                                                        |
| T009 | T003, T004, T006, T007, T008                                      |
| T010 | T001, T009                                                        |
| T011 | T009, T010                                                        |
| T012 | T009, T010, T011                                                  |

US1 precedes completed US2 validation; T005/T008 can start while project owners
work on separate files. Never parallelize changes to the same workflow/helper.
Feature 002 consumes the foundation after T012; do not duplicate this scaffolding
in its backlog. Outstanding 001 hosted/audit validation is not a prerequisite
for authoring this plan, nor evidence those checks passed.

## Coverage and delivery strategy

| Requirement | Tasks                        |
| ----------- | ---------------------------- |
| FR-001      | T001, T002, T003, T004, T012 |
| FR-002      | T002, T003, T012             |
| FR-003      | T002, T004, T012             |
| FR-004      | T005, T006, T007, T012       |
| FR-005      | T005, T007, T012             |
| FR-006      | T002, T012                   |
| FR-007      | T001–T010, T012              |
| FR-008      | T011, T012                   |
| FR-009      | T002, T003, T004, T011, T012 |
| SC-001      | T002, T003, T004, T012       |
| SC-002      | T005, T006, T007, T012       |
| SC-003      | T006–T010, T012              |
| SC-004      | T005, T007, T012             |
| SC-005      | T002, T012                   |
| SC-006      | T011, T012                   |

The smallest useful increment is US1's neutral project ownership, but it is not
feature completion: external tests, truthful CI, security checks, documentation,
and final acceptance are required. No deployment increment is authorized.

Twelve tasks total: three US1, three US2, one setup/review, and five cross-cutting.
No completed checkbox or issue is implied by generating this backlog.
