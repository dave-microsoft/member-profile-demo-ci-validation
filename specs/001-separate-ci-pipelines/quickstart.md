# CI Foundation Validation

These validation scenarios distinguish historical local evidence, verified
partial hosted evidence, and outstanding validation.

## Local prerequisites

Use Node.js 22.17+ and the internal npm registry configured by `.npmrc`.
No Azure credentials or application SDKs are needed.

```powershell
node --test tests/unit/tooling/ci-foundation.test.mjs
npm ci --ignore-scripts --audit=false --fund=false
npm run test:tooling
npm run lint
npm audit --audit-level=high
```

Expected: focused tests cover path selection, failed comparisons, missing
capabilities, and dependency-policy rejection; existing tooling checks execute.
An unreachable external Markdown link is a failure, not an ignored success.

## Hosted scenarios

To complete T007, inspect real Actions runs for documentation-only,
shared/configuration, synthetic application-only and infrastructure-only changes,
and cross-domain renames/deletions. Do not merge synthetic project fixtures.

Expected: both named checks start and finish; documentation-only changes skip
npm entirely, while source/unit-test or supporting configuration changes run
tooling once; affected absent
project validation blocks rather than claiming success. Unknown synthetic
package identities stop before public requests without printing their names.

The stable result jobs are **Application CI result** and
**Infrastructure CI result**. Do not select unrelated workflow status names
when configuring required checks.

Manual workflow dispatch is validation only, not a deployment route. Confirm
that fork PRs have no secrets, cloud authentication, or write permissions.

## Limitations

Repository permissions are needed to configure required check names. Do not
change branch protections or claim they are configured as part of local tests.
Future app/Bicep checks and CD activation need approved projects, targets,
identities, executable validation, and environment approvals.

## Current evidence

At the earlier implementation checkpoint, the focused acceptance suite was
observed failing before implementation and then passed eight tests; the combined
tooling suite passed twelve. Local lint, formatting, link checks, workflow YAML
parsing, and internal-feed restore passed. Lock comparison confirmed unchanged
versions/integrity. These are historical results, not checks rerun by this update.

At the later T009 checkpoint on 2026-11-16, two regressions failed before the
latency fix, then all 13 combined tooling tests passed, including a docs-only
entrypoint invocation without package files. Markdown lint, formatting, and diff
checks passed then; no full external-link or audit rerun was claimed.

**PARTIAL hosted evidence — verified read-only on 2026-11-16**:
[Application CI](https://github.com/dmd0822/member-profile-demo/actions/runs/43913848561)
and [Infrastructure CI](https://github.com/dmd0822/member-profile-demo/actions/runs/43913848563)
are completed/successful `main` push runs at commit
`459188c3fa60c47b90c27fd8f351aca145dc54d8`. The **Application CI result** and
**Infrastructure CI result** jobs respectively completed successfully. This
verifies terminal jobs for those runs, not T007's full hosted scenario matrix,
Node.js 22 runtime behavior, public-registry retrieval, fork permissions, or
required-check configuration. T007 remains open.

The local approved-feed audit is blocked: initial DNS failure followed by HTTP
429 on a bounded recheck. The application CI entrypoint reports failure for this
condition, without falling back to public npm. Successful hosted terminal jobs
do not clear this local audit blocker: T008 and dependent T006 remain open.
Public npm retrieval remains unverified by this update. Historical local
evidence uses available Node.js 26, not the Node.js 22 runner configured by the
workflows. See [task evidence](tasks.md).

A final local lint recheck reported zero broken links but its link-check process
lingered after printing results and was stopped. That invocation is not a passing
exit; the partial hosted evidence above does not change that local outcome
or complete the remaining hosted validation.
