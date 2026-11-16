# Member Profile Application Design Review

**Status: Proposed.** This review captures alternatives and questions for
architecture, product, security, and operations review. No option below is an
accepted or implemented decision.

**Planning status (2026-11-16):** The user acting as product owner and tech lead
approved both feature specifications for planning only. The
[002 demo plan](../../specs/002-member-profile-demo/plan.md) and
[003 initial-project plan](../../specs/003-initial-app-projects/plan.md) remain
proposed, not design or security approvals or authorization to implement or deploy.

For the scoped demo proposal, see [demo research](../../specs/002-member-profile-demo/research.md):
business changes and required internal audit records share one SQL transaction;
queue/central-audit delivery and its worker remain a separately reviewed direction,
not required or approved demo scope. Telemetry is proposed server-only because
the Application Insights JavaScript SDK does not support Entra-authenticated
ingestion. No direct browser telemetry workaround or local authentication is
permitted.

For current requirements and the conceptual system boundary, see the
[architecture overview](member-profile-overview.md).

## Decisions to review

### API hosting

The backend uses C#/.NET 11. The current working choice is Azure Container
Apps; the Angular web UI is also planned as a separate Container App. Each
environment has its own Container Apps Environment. This keeps the initial
design on the simplest one-API architecture; there is no requirement for
microservices or independent business services.

| Direction                                                 | Benefits                                                                                                                                                                                                              | Trade-offs and validation                                                                                                                                                                 |
| --------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Separate Azure Container Apps for Angular UI and .NET API | Supports the conventional containerized API preference, direct browser-to-API access, and environment-specific deployment. The landing zone supplies networking; the shared registry avoids an application-owned ACR. | Requires image build/promotion and ACR access. Public ingress is exposed without the future WAF. Validate cold starts, CORS, authentication, private SQL access, and production recovery. |

**Review position:** Container Apps is the working hosting choice, not a
deployed or fully approved decision. Scale UI and API to zero to reduce idle
cost for synthetic-data environments; validate startup behavior against the
eventual production latency target and expected peak load.

See the [Azure hosting and data infrastructure design](azure-infrastructure.md)
for the proposed topology and landing-zone integration boundary.

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

Microsoft documents North Central US as paired with South Central US. For the
synthetic-data phase, SQL serverless auto-pause is selected to reduce idle
cost, and recovery may reset a known seed dataset even if it takes longer than
two hours.

This differs from the eventual production target: SQL serverless auto-pause
cannot be combined with active geo-replication or failover groups. A production
SQL tier, backup redundancy, and regional recovery design must be selected and
tested before real-data use. Geo-restore can take minutes or hours and does
not itself guarantee the two-hour application RTO/RPO. See the [Azure hosting
and data infrastructure design](azure-infrastructure.md).

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

| Topic                        | Current state                                                                                                                     | Needed to close                                                                                                                                             |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| API and UI hosting           | Separate UI/API Container Apps, one Container Apps Environment per application stage. Use the shared ACR and generated hostnames. | Validate generated-hostname identity redirects, CORS, image promotion, cold starts, and the production p95 target.                                          |
| Public edge                  | Direct public HTTPS ingress without a managed WAF in the initial synthetic-data phase.                                            | Decide WAF, custom domain, DNS, identity redirect URIs, and the security gate for real-data use.                                                            |
| Member identity              | Demo requires member email one-time-code sign-in; Entra External ID remains a candidate.                                          | Select a provider; verify its passwordless flow, configuration, cost, lifecycle, and data location.                                                         |
| Profile claim and enrollment | Staff verification required; enrollment may claim an existing or create a new profile.                                            | Define production evidence, staff verification steps, duplicate handling, and contested-claim process.                                                      |
| Contact and identity email   | Profile contact email is separate from sign-in email; changing it does not change sign-in.                                        | Decide whether sign-in email can change and define any re-verification needs.                                                                               |
| Membership model             | Start date required; optional end date not before it. Demo types/statuses may be placeholders.                                    | Define production types/statuses, remaining date rules, number format, and allowed member-request operations.                                               |
| Review integration           | Demo review is simulated; staff manually record approval or rejection. Production remains open.                                   | Decide production ticketing platform, status synchronization, failure handling, and who records outcomes.                                                   |
| API consumers                | Web application first; other consumers anticipated.                                                                               | Identify future consumer trust boundaries and token/authorization model before external onboarding.                                                         |
| Scale and performance        | Unknown.                                                                                                                          | Estimate profile count, peak traffic, and load shape; validate the constitutional latency target.                                                           |
| Availability and operations  | Availability target and operator are undecided.                                                                                   | Identify service owner, support expectations, and availability objective.                                                                                   |
| Recovery                     | Production RTO and RPO are each roughly two hours; paired US region is acceptable.                                                | Confirm approved region/service availability, tier, failover process, full-stack runbooks, and test evidence.                                               |
| SQL idle cost and recovery   | Serverless SQL auto-pause across environments; demo recovery can reseed synthetic data and exceed two hours.                      | Select and exercise a production SQL/recovery configuration; serverless auto-pause conflicts with continuous geo-replication.                               |
| Landing-zone boundary        | Landing zone provides network, DNS, shared ACR, and SQL/queue private endpoints.                                                  | Confirm subnet capacity, egress, RBAC, private DNS integration, and who provisions each dependency in the deployment sequence.                              |
| Audit event buffer           | Azure Storage Queue; retain until central API acknowledgment; central API deduplicates by event ID; alert after three hours.      | Define cross-store outcome reconciliation, poison-message handling, queue redundancy, monitoring, and central API contract.                                 |
| Privacy and retention        | Inactive profiles retained; retention duration open. DOB collected; minor-member scope unknown.                                   | Privacy/legal review for retention, minors, deletion rights, and future international requirements.                                                         |
| Residency                    | Initial US-only; eventual non-US residence may be needed.                                                                         | Current constitution is US-only. Obtain required governance and security approval before changing that constraint or deploying outside approved US regions. |
| Cost                         | Budget undecided; optimization is a priority.                                                                                     | Establish budget guardrails and compare full lifecycle and recovery costs.                                                                                  |

## Review status

All design directions in this document remain proposed. Product owner and tech
lead specification approval for planning is recorded above; applicable human
design, architecture, security, privacy, and operations reviews remain pending.
This document grants no additional Spec Kit authorization and does not authorize
application scaffolding, implementation, or infrastructure deployment.
