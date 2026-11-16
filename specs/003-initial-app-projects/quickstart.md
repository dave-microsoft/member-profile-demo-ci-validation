# Validate the initial application foundation

## Status and purpose

This is a **planned future validation how-to** for contributors and reviewers of
003 Initial Application Projects, limited to US1/US2, FR-001–FR-009, and
SC-001–SC-006. It is not a guide to running the member-profile demo.

The specification has product owner and tech lead approval for planning only.
The project shapes, test tooling, scripts, and commands below are proposals,
not implemented or tested entry points. This document does not approve their
design or authorize scaffolding, dependency retrieval, implementation, or
deployment. Do not execute the future procedure until the gates below are met.

The foundation lets contributors locate and validate one frontend and one
backend without requiring the other application or cloud services. It adds no
member behavior, enrollment, profile management, authentication, database flow,
API behavior, or deployment.

## 1. Complete the review gates

Before adding packages or implementing the foundation:

1. Obtain design and test-tooling review of the proposed project shapes,
   independent checks, and external frontend test discovery.
2. Record exact package patches and the exact .NET SDK pin, including the future
   `global.json`. Obtain human review of the public/private dependency inventory,
   licenses, and vulnerabilities **before adding packages**.
3. Verify approved feed identity and access. Local npm must explicitly use
   `https://packagefeedproxy.microsoft.io/npm/`. Private dependencies and all
   non-npm packages must use approved internal feeds in every environment.
   The non-npm feed identity and access are unverified: stop before restore
   while either is missing. Never fall back to nuget.org or another registry.
4. Review the feature plan, task backlog, consistency analysis, and linked
   milestone/issues. This guide is not evidence that implementation gates have
   completed.
5. Obtain explicit later implementation authorization. Planning approval alone
   does not permit the following work.

Proposed compatibility baseline:

- Angular and Angular CLI 22.
- Node.js 24.15.x or a later compatible 24.x release, not unrestricted later
  major versions.
- TypeScript 6.0.x and RxJS 7.8.x.
- .NET 11, with the exact SDK version still to be pinned.

The [Angular compatibility table](https://angular.dev/reference/versions) was
verified on 2026-11-16 for this planning baseline. Compatibility ranges are not
approved exact dependency pins or proof that this repository builds.

## 2. Establish fail-first acceptance checks

After implementation authorization, write acceptance checks before scaffolding
and record their expected failures against the absent foundation. A missing
tool is a prerequisite failure, not evidence that an acceptance assertion failed.
Do not install packages merely to follow this planning document.

Propose Node-native acceptance checks under `tests/unit/foundation/`, exposed
through a future root `npm run test:foundation` script. That script does not
exist today. Checks must assert project counts, source/test boundaries,
forbidden references, and the absence of speculative shared libraries,
business endpoints, extra runtimes, and infrastructure.

Use temporary synthetic negative fixtures or isolated changes, never real
member data. Include a passing control and verify that each prohibited case
fails for the intended reason; remove the negative fixture afterward.

The proposed MSTest project must exercise meaningful backend foundation
constraints: `net11.0`, permitted project/dependency references, and source/test
locations. Do not substitute a dummy passing test or fabricated coverage.

## 3. Check project and test ownership

The proposed implementation has these responsibilities:

| Location                                                | Expected responsibility                                                                        |
| ------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `src/frontend/`                                         | One Angular workspace with local build/test scripts.                                           |
| `src/frontend/src/app/components/`                      | Reusable UI component location; no member behavior in this feature.                            |
| `src/frontend/src/app/pages/`                           | Reserved ownership for routed views, not implemented member flows.                             |
| `src/frontend/src/app/services/`                        | Frontend service ownership, not speculative API clients.                                       |
| `src/backend/MemberProfile.Backend.csproj`              | One host-neutral `Microsoft.NET.Sdk` class library targeting `net11.0`.                        |
| `src/backend/Api/`, `Services/`, `Models/`              | Distinct backend responsibility areas; no endpoints, business services, or entities to invent. |
| `tests/unit/frontend/`                                  | Frontend tests physically outside `src/`.                                                      |
| `tests/unit/backend/MemberProfile.Backend.Tests.csproj` | Supporting MSTest project, not another runtime application.                                    |
| `tests/unit/foundation/`                                | Node-native project and boundary acceptance checks.                                            |

The backend proposal includes no server, ASP.NET hosting, member logic, or
additional runtime project. Neither application needs the other running to
build or test.

Angular's default Vitest/jsdom tooling with the CLI builder is proposed.
Discovery and execution of tests in `tests/unit/frontend/` **must be validated
on the pinned versions before the approach is accepted**. Demonstrate that
external tests are discovered and that an intentionally failing test fails the
command. If unsupported, stop for test-tooling review; do not silently colocate
tests under `src/`.

No persistence, entities, or interfaces are required, so this scope creates no
data-model or contract artifacts. Do not create `src/shared/`,
`tests/integration/`, `tests/contract/`, or `infra/` without an applicable approved
need. Their layout rules still apply if a later feature needs them. In
particular, Angular must not reference C# implementation libraries.

## 4. Validate each project independently

All commands in this section are **future, untested proposals**, run from the
repository root in PowerShell after approved implementation. They are not
available setup instructions for the current checkout.

Run each command separately and stop on any nonzero exit code. Future scripts
and CI wrappers must propagate failures, including missing tools, missing or
zero discovered tests, and failed builds. A successful process with no tests
is not acceptance evidence.

### Frontend

After inventory review and approval of the frontend's separate lockfile, a
proposed local restore is:

```powershell
npm --prefix src/frontend ci --ignore-scripts --audit=false --fund=false --registry=https://packagefeedproxy.microsoft.io/npm/
```

Stop on feed or access failure; do not switch to public npm locally. Audit
explicitly against the internal registry only after dependency identity/source
validation. An unavailable audit service is a blocked check. If a pinned build
tool requires an install script, review that exact script before enabling it;
do not blindly drop `--ignore-scripts`.

Then validate the frontend without starting or building the backend:

```powershell
npm --prefix src/frontend run build
npm --prefix src/frontend run test -- --watch=false
```

Confirm nonzero discovery counts from `tests/unit/frontend/`, not just a
successful exit. The reviewed test configuration must prove that jsdom tests
actually execute outside `src/`.

### Backend

Do not run restore until approved internal non-npm feeds are explicitly
configured and verified, with no public fallback source. After that gate, the
proposed commands are:

```powershell
dotnet restore src/backend/MemberProfile.Backend.csproj
dotnet restore tests/unit/backend/MemberProfile.Backend.Tests.csproj
dotnet build src/backend/MemberProfile.Backend.csproj --configuration Release --no-restore
dotnet build tests/unit/backend/MemberProfile.Backend.Tests.csproj --configuration Release --no-restore
dotnet test tests/unit/backend/MemberProfile.Backend.Tests.csproj --configuration Release
```

The test command can restore implicitly; the same approved internal-only source
configuration must govern that operation. Verify actual MSTest discovery and
meaningful constraint assertions. Run without the frontend, cloud credentials,
database, or running service.

### Foundation acceptance

Once its script and checks exist, run the proposed root entry point:

```powershell
npm run test:foundation
```

This is separate from the current `npm run test:tooling`, which tests repository
tooling rather than either proposed application.

## 5. Exercise the acceptance scenarios

Record command exit codes, test discovery counts, and the assertions exercised.
These are planned scenarios, not reported results.

| Scenario                    | Expected evidence and negative case                                                                                                                                                               | Trace                                   |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
| Locate the initial projects | Exactly one frontend and one backend application project; all application source has the correct owner and distinct responsibility areas. An extra runtime or misplaced source fails validation.  | US1; FR-001–FR-003; SC-001              |
| Keep tests external         | Every unit test is under the matching `tests/unit/` service area. A test under `src/` fails. Any later integration/contract test must use its matching category; do not create unused suites now. | US2; FR-004; SC-002                     |
| Keep projects independent   | Each build and its nonempty test suite succeeds without the other application or cloud. A missing tool, missing/zero tests, or deliberate build/test failure exits nonzero.                       | US1/US2; FR-007; SC-003                 |
| Prevent speculative sharing | No unapproved shared library or frontend reference to backend implementation. Introduce each prohibited case in isolation and confirm rejection.                                                  | US2; FR-005; SC-004                     |
| Separate infrastructure     | No runtime files in `infra/`, no infrastructure definitions in either application, and no resources provisioned. Synthetic misplaced files fail boundary checks.                                  | US1/US2; FR-006; SC-005                 |
| Keep guidance truthful      | Review every delivered setup/build/test command against actual files and execution evidence. A nonexistent script, untested command presented as working, or accepted-hosting claim fails review. | US1/US2; FR-008–FR-009; SC-006          |
| Reject feature creep        | No member/business endpoint, authentication, production data access, deployment, or extra runtime is introduced by this foundation. Reject such additions rather than demonstrating a demo flow.  | US1/US2; FR-001, FR-009; SC-001, SC-006 |

## 6. Verify CI activation in the implementation PR

Currently, `.github/scripts/ci-foundation.mjs` readiness always reports
`implemented: false`; application source changes block the current application
gate until executable checks are added. Documentation-only results do not prove
application readiness.

The future implementation PR must activate the applicable build/test checks in
the same PR as the projects:

- Preserve the stable **Application CI result** and **Infrastructure CI result**
  jobs and separate workflows. Do not add workflow-level path filters.
- Select required checks for source, test, configuration, CI, and deletion
  changes. Demonstrate that deleting a project or test cannot evade validation.
- Derive readiness from real executable checks, not blanket
  `implemented: true`. Propagate nonzero failures; missing or zero tests must
  fail, and unexecuted checks must never appear passed.
- Review the frontend lockfile separately: the current root dependency
  validator rejects npm workspaces. Do not assume it validates the nested
  application dependency tree.
- Use public npm in GitHub Actions only for reviewed public package identities.
  Keep private npm and all non-npm dependencies on approved internal feeds.
  Feed failure must stop retrieval without fallback or disclosure of private
  identities.
- Use no cloud credentials and activate no deployment. An inapplicable
  infrastructure check is not an implemented infrastructure validator.

SAST and secret scanning are presently unavailable, not passing. Required
security/dependency checks and human review remain gates; successful foundation
builds alone cannot establish full CI or merge readiness.

## 7. Record limits and hand off

The current root validation entry points relevant here are
`npm run lint:markdown`, `npm run format:check`, `npm run lint:links`, and
`npm run test:tooling`. They do not build or test an application.

After implementation, verify documentation accuracy and cross-links with the
existing documentation checks. Report their actual scope and results; do not
retrieve dependencies merely to make this planning guide appear validated.
Keep README/status guidance and artifact/tracking consistency aligned, including
checking this guide against the completed plan and research.

Record these limits explicitly:

- **API latency:** p95 under 300 ms is not applicable here: no API behavior or
  expected peak load is defined. Do not invent a benchmark.
- **Coverage:** business-logic coverage is not applicable because this foundation
  has no business logic; do not report 100%. The future minimum 80% business-logic
  coverage and public-endpoint contract-test gates remain in force.
- **Accessibility:** shell checks and jsdom tests do not validate real-browser
  or screen-reader behavior and establish no accessibility conformance.
- **Design:** no hosting, identity, persistence, or deployment option is accepted
  by this guide. Demo design review belongs to the separate architecture work.

If a prerequisite, approved feed, exact pin, or test-discovery mechanism is
missing, stop the affected validation and report it as blocked. Do not replace
it with a dummy check, an unapproved package source, or a claim of success.

## Sources and companion artifacts

- [Initial Application Projects specification](spec.md): scope and acceptance.
- [Feature implementation plan](plan.md): companion planning output; review and
  reconcile before implementation.
- [Feature research](research.md): source evidence and proposed design rationale.
- [Authoritative constitution](../../.specify/memory/constitution.md): quality,
  dependency, layout, and approval requirements.
- [Repository contributor guidance](../../README.md): current repository context.
- [CI foundation validation guide](../001-separate-ci-pipelines/quickstart.md):
  separate CI-foundation scope and evidence limitations.
