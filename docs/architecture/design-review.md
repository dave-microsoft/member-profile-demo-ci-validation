# Member Profile Application Design Review

**Status: Proposed.** This review captures alternatives and questions for
architecture, product, security, and operations review. No option below is an
accepted or implemented decision.

For current requirements and the conceptual system boundary, see the
[architecture overview](member-profile-overview.md).

## Decisions to review

### API hosting

The backend must use C#/.NET 11. A single API is the simplest starting point;
there is no current requirement for microservices, a message broker, or
independent service deployment.

| Option                           | Benefits                                                                                                                                                                                                      | Trade-offs and validation                                                                                                                                                                                                                           |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Azure Functions Flex Consumption | Managed serverless hosting; supports .NET 11, VNet integration, and optional always-ready instances. Its execution-based model is a candidate for uncertain or intermittent demand and a cost-conscious demo. | HTTP API suitability, startup behavior, operational diagnostics, API authorization, and p95 latency must be tested. Always-ready capacity reduces cold starts but changes cost. Validate private SQL connectivity and the required recovery design. |
| Azure Container Apps             | Managed container hosting suited to API endpoints; supports container revisions and scaling, including scale-to-zero for many workloads. It accommodates a conventional ASP.NET Core API deployment.          | Requires a container image and its build, registry, and revision lifecycle. Minimum replicas and regional standby affect cost. Validate VNet design, scaling, warm-up, and recovery behavior.                                                       |

**Review position:** Keep both viable until expected peak load, availability,
operations ownership, and cost constraints are clearer. A small, measured
prototype can compare startup, p95 latency, private SQL access, and operational
effort without committing to production infrastructure.

Official references: [Functions Flex Consumption](https://learn.microsoft.com/en-us/azure/azure-functions/flex-consumption-plan)
and [Azure Container Apps overview](https://learn.microsoft.com/en-us/azure/container-apps/overview).

### Member identity and enrollment

The initial demo requires members to sign in using an email one-time code. The
member identity provider remains unselected; Microsoft Entra External ID is a
candidate because it provides customer identity capabilities and documents
one-time-passcode sign-in options. Email codes can also be a multifactor
authentication step in some flows; verify the exact passwordless configuration,
tenant type, lifecycle, cost, and data location before selecting it.

Staff identity remains in the workforce Entra tenant. A designated Entra group
grants the staff permissions described in the overview.

The account-claim rule is security-critical: authenticating access to an email
account does not by itself prove entitlement to a staff-created profile.
Membership and member numbers support staff lookup but are not authenticators.
The production process must define the evidence and staff action required to
link a member identity to a profile, prevent duplicate claims, and handle
mistaken or disputed matches. Until staff verification succeeds, the member can
see only their own submitted enrollment details and cannot edit profile data.

Official references: [Microsoft Entra External ID overview](https://learn.microsoft.com/en-us/entra/external-id/external-identities-overview)
and [identity providers for external tenants](https://learn.microsoft.com/en-us/entra/external-id/customers/concept-authentication-methods-customers).

### API and network boundary

The web application is public, and the API must be reachable by its users over
HTTPS. Azure SQL remains private: use a private endpoint, disable public network
access, and allow access from the API through private networking and managed
identity. The API must perform authorization for every resource operation;
frontend route guards are not a security boundary.

The web application is the first API consumer. Future consumers require an
explicit authentication and authorization design, versioned contracts, and
rate-limit and governance requirements. An API gateway is not selected;
evaluate one only when concrete consumer or policy needs justify its added
operational and cost footprint.

### Data and review workflow

The application is the source of truth for contact and membership data. Keep
member, membership, enrollment, and membership-change request concepts
separate. Generate member and membership numbers in the application, and
enforce uniqueness transactionally. Number format, sequence strategy, and
concurrency behavior are open design details.

A member can have multiple memberships. Each requires a start date; an optional
end date cannot precede it. The demo uses placeholder membership types and
statuses; production definitions remain open.

Verified members can directly update only profile contact email, phone, and
mailing address; staff handle changes to name and date of birth. Profile
contact email is separate from sign-in email, and changing contact email does
not change sign-in.

Membership changes are submitted as requests. For the demo, staff manually
record the simulated external review's approval or rejection and apply only
approved changes using staff screens. The requesting member can see the request
status and any recorded outcome. Before production, decide the ticketing
product, API contract, authentication, retry and duplicate handling, failure
recovery, and how request status and outcome return to the application. The
application's request status must not imply external approval unless the review
result has been recorded.

Ended memberships and inactive profiles remain stored, but the retention
period, deletion exceptions, and obligations for international members remain
open. Legal/privacy reviewers must determine how future GDPR-related
requirements affect access, correction, export, erasure, retention, and
residency.

### Database recovery and regions

Microsoft documents North Central US as paired with South Central US. A
paired-region database replica or Azure SQL failover group is a candidate for
the production recovery target. Geo-replication introduces cost and can involve
replication lag; the permitted data loss must be verified under the selected
configuration.

The two-hour targets apply to the entire application, including application
hosting, frontend delivery, traffic routing, identity dependencies, network
access, and human procedures. A database failover alone does not meet the
workload objective. Compare active/passive and active/active only after
availability, load, cost, and operations requirements are known. With current
information, active/passive is the simpler candidate, not an accepted decision.

Official references: [Azure region pairs](https://learn.microsoft.com/en-us/azure/reliability/regions-paired),
[Azure SQL failover groups](https://learn.microsoft.com/en-us/azure/azure-sql/database/failover-group-sql-db),
and [Azure SQL business continuity guidance](https://learn.microsoft.com/en-us/azure/azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview).

## Cross-cutting review

| Well-Architected pillar | Review focus                                                                                                                                                                                                                         |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Security                | Validate identity configuration, staff group lifecycle, member-to-profile authorization, safe account linking, private SQL access, encryption and key requirements, sensitive DOB handling, and PII exclusion from logs and prompts. |
| Reliability             | Define availability and support expectations; design and exercise complete paired-region recovery; cover geo-replication lag, restore procedures, retries, and external review integration failures.                                 |
| Performance efficiency  | Establish expected profile volume and peak concurrent/API load before sizing. Measure the p95 API target below 300 ms at that agreed load, including identity and database dependencies.                                             |
| Cost optimization       | Set a budget or cost guardrail; estimate API hosting, SQL tier, identity, telemetry, network, backup, and secondary-region costs. Compare warm capacity with scale-to-zero behavior.                                                 |
| Operational excellence  | Identify service owner, on-call/support model, deployment approvals, monitoring and alerting ownership, audit access, and recovery exercise cadence. Keep infrastructure changes in Bicep and pipelines.                             |

## Open questions and required reviews

| Topic                            | Current state                                                                                   | Needed to close                                                                                                                                             |
| -------------------------------- | ----------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| API hosting                      | Functions Flex Consumption and Container Apps remain candidates.                                | Expected traffic, p95 measurements, operations ownership, and cost comparison.                                                                              |
| Frontend hosting and public edge | Not selected.                                                                                   | Confirm delivery, custom-domain, identity redirect, availability, and security needs, then compare supported hosting options.                               |
| Member identity                  | Demo requires member email one-time-code sign-in; Entra External ID remains a candidate.        | Select a provider; verify its passwordless flow, configuration, cost, lifecycle, and data location.                                                         |
| Profile claim and enrollment     | Staff verification required; enrollment may claim an existing or create a new profile.          | Define production evidence, staff verification steps, duplicate handling, and contested-claim process.                                                      |
| Contact and identity email       | Profile contact email is separate from sign-in email; changing it does not change sign-in.      | Decide whether sign-in email can change and define any re-verification needs.                                                                               |
| Membership model                 | Start date required; optional end date not before it. Demo types/statuses may be placeholders.  | Define production types/statuses, remaining date rules, number format, and allowed member-request operations.                                               |
| Review integration               | Demo review is simulated; staff manually record approval or rejection. Production remains open. | Decide production ticketing platform, status synchronization, failure handling, and who records outcomes.                                                   |
| API consumers                    | Web application first; other consumers anticipated.                                             | Identify future consumer trust boundaries and token/authorization model before external onboarding.                                                         |
| Scale and performance            | Unknown.                                                                                        | Estimate profile count, peak traffic, and load shape; validate the constitutional latency target.                                                           |
| Availability and operations      | Availability target and operator are undecided.                                                 | Identify service owner, support expectations, and availability objective.                                                                                   |
| Recovery                         | Production RTO and RPO are each roughly two hours; paired US region is acceptable.              | Confirm approved region/service availability, tier, failover process, full-stack runbooks, and test evidence.                                               |
| Privacy and retention            | Inactive profiles retained; retention duration open. DOB collected; minor-member scope unknown. | Privacy/legal review for retention, minors, deletion rights, and future international requirements.                                                         |
| Residency                        | Initial US-only; eventual non-US residence may be needed.                                       | Current constitution is US-only. Obtain required governance and security approval before changing that constraint or deploying outside approved US regions. |
| Cost                             | Budget undecided; optimization is a priority.                                                   | Establish budget guardrails and compare full lifecycle and recovery costs.                                                                                  |

## Review status

All design directions in this document remain proposed. Product, architecture,
security, privacy, and operations reviews are still needed as applicable. This
document does not authorize application scaffolding, implementation planning,
infrastructure deployment, or a Spec Kit workflow step.
