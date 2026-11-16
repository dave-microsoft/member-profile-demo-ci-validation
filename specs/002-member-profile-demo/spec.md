# Feature Specification: Member Profile Demo

**Feature Branch**: `dave-microsoft-application-specifications`

**Created**: 2026-11-16

**Status**: Draft; product owner and tech lead review required

**Input**: User description: "I want to start the planning phase for the appicaltion. We need to create the specs."

The first specification covers the complete initial demo: enrollment,
verification, member and staff profile management, memberships, and membership
change requests. The demo uses synthetic member data and simulated review
handoffs. This is a proposed feature, not an implemented or approved system.
The [architecture overview](../../docs/architecture/member-profile-overview.md)
provides context; the [constitution](../../.specify/memory/constitution.md)
remains authoritative.

## User Scenarios & Testing _(mandatory)_

### User Story 1 - Start Enrollment Safely (Priority: P1)

As a prospective member, I want to sign in and submit my enrollment so staff can
verify my association with a profile without exposing someone else's data.

**Why this priority**: Enrollment is the entry point, and an unverified account
must not gain access to an existing profile.

**Independent Test**: Using synthetic member accounts and data, sign in, submit
an enrollment, and check what the applicant and a different account can access.

**Acceptance Scenarios**:

1. **Given** a prospective member, **When** they sign in using an email one-time code and submit their name, contact email, phone, date of birth, and mailing address, **Then** an enrollment is recorded for that account as pending verification.
2. **Given** an invalid, expired, or previously used code, **When** a member attempts to sign in with it, **Then** the member is not authenticated.
3. **Given** a pending enrollment, **When** its applicant opens it, **Then** they can see their submitted enrollment details but cannot view a pre-existing profile or edit profile data.
4. **Given** a pending enrollment, **When** another member attempts to access it, **Then** the enrollment details are not disclosed.
5. **Given** a missing enrollment field, **When** the applicant submits the enrollment, **Then** the missing field is identified and the enrollment is not accepted.
6. **Given** only an email address, member number, or membership number, **When** an applicant attempts to claim an existing profile without staff verification, **Then** no profile link, profile access, or confirmation that the profile exists is provided.

### User Story 2 - Verify an Enrollment (Priority: P1)

As an authorized staff member, I want to record a simulated verification and
link the applicant to the correct existing profile or create a new profile, so
the member can access only their own information.

**Why this priority**: Staff verification is the boundary between pending
enrollment and access to a member profile.

**Independent Test**: With a staff account and synthetic enrollments, record
verification for an existing profile and, separately, for a new profile.

**Acceptance Scenarios**:

1. **Given** a pending enrollment, **When** authorized staff locate it, **Then** they can view its submitted details to record the simulated verification.
2. **Given** a pending enrollment and a simulated verification for an existing profile, **When** authorized staff record the verified link, **Then** that applicant can view the linked profile.
3. **Given** a pending enrollment and a simulated verification for a new profile, **When** authorized staff record approval and create the profile from the enrollment, **Then** the application assigns a member number and makes the profile available to that applicant.
4. **Given** a profile already linked to a different verified member account, **When** staff attempt a conflicting link, **Then** the link is not created and the applicant does not gain access.
5. **Given** an account without the designated staff entitlement, **When** it attempts to verify or link an enrollment, **Then** the action is denied.
6. **Given** simulated verification has not been recorded, **When** an applicant presents a matching number or email address, **Then** those details alone do not authorize a profile link.

### User Story 3 - View and Update My Profile (Priority: P1)

As a verified member, I want to see my profile and memberships and update my
contact information, while my sign-in identity remains separate.

**Why this priority**: Members need a useful, private self-service experience
after verification.

**Independent Test**: Sign in as a verified synthetic member, view the linked
profile, update each permitted contact field, and attempt a restricted update.

**Acceptance Scenarios**:

1. **Given** a verified member, **When** they open their profile, **Then** they can see their name, contact email, phone, date of birth, mailing address, and associated memberships, including ended memberships.
2. **Given** a verified member, **When** they update their contact email, phone, or mailing address, **Then** the permitted changes are saved to their own profile.
3. **Given** a verified member changes their contact email, **When** they next sign in, **Then** their sign-in email is unchanged.
4. **Given** a verified member, **When** they attempt to directly change their name, date of birth, or membership details, **Then** the change is denied and the stored data remains unchanged.
5. **Given** another member's profile or membership identifier, **When** a member attempts to access it, **Then** the other member's information is not disclosed.

### User Story 4 - Protect and Account for Changes (Priority: P1)

As a reviewer, I want the demo to protect member information and account for
changes so its behavior can be assessed without exposing personal data.

**Why this priority**: Privacy, authorization, and auditability apply to every
demo journey, not only to a later production system.

**Independent Test**: Exercise representative successful and unsuccessful
changes with synthetic data, inspect audit and error information, and review
the demo's data protections.

**Acceptance Scenarios**:

1. **Given** an authenticated actor performs a state-changing operation, **When** it succeeds, **Then** an internal audit event records the actor reference, operation or changed field names, time, and outcome without storing profile values.
2. **Given** an attempted change fails while audit recording is available, **When** the outcome is recorded, **Then** the audit event records the failure without storing profile values.
3. **Given** the required audit event cannot be recorded, **When** a change is attempted, **Then** the change is not committed as a successful unaudited operation and the failure is reported.
4. **Given** an operation fails, **When** its error is shown, **Then** it includes a reference identifier without exposing a stack trace or member data.
5. **Given** the demo is prepared or exercised, **When** its member records and test data are reviewed, **Then** they are synthetic and do not identify real people.
6. **Given** demo data is stored or transmitted, **When** its protections are reviewed, **Then** access is restricted, data is encrypted, and stored data remains in approved US regions.
7. **Given** an operation produces logs or traces, **When** they are inspected, **Then** they contain no personal information, account data, authentication codes, tokens, or request and response payloads.

### User Story 5 - Use Key Flows Accessibly (Priority: P1)

As a person who uses a keyboard or assistive technology, I want to navigate the
demo and understand its fields and errors so I can complete the same tasks as
other members and staff.

**Why this priority**: Enrollment and profile management must be usable without
requiring a mouse or relying on visual cues alone.

**Independent Test**: Complete the member and staff journeys using a keyboard
and check field labels and errors with assistive technology.

**Acceptance Scenarios**:

1. **Given** a keyboard-only user, **When** they navigate the member and staff workflows, **Then** they can reach and operate every required control and see which control has focus.
2. **Given** a form field, **When** a user encounters it, **Then** its label and instructions are available visually and to assistive technology.
3. **Given** a form validation error, **When** a user encounters the affected field, **Then** the error identifies the field in text and is available to assistive technology without relying on color alone.
4. **Given** a member is entering a one-time sign-in code, **When** they paste the code, **Then** the entry accepts it.

### User Story 6 - Maintain and Find Profiles (Priority: P2)

As an authorized staff member, I want to create, find, and update profiles so
staff can maintain the application's member records.

**Why this priority**: Staff need to maintain records and find the correct
profile when supporting members or verifying an enrollment.

**Independent Test**: Create and update synthetic profiles, then search for
them using each supported identifier.

**Acceptance Scenarios**:

1. **Given** authorized staff creating a profile, **When** they save its member details, **Then** the application generates a member number unique among profiles.
2. **Given** existing synthetic profiles and memberships, **When** authorized staff search by member number, membership number, name, or contact email, **Then** matching profiles are available to staff.
3. **Given** an existing profile, **When** authorized staff update its name, contact email, phone, date of birth, or mailing address, **Then** the permitted change is saved.
4. **Given** an account without the designated staff entitlement, **When** it attempts a staff search or profile update, **Then** access is denied.

### User Story 7 - Maintain Memberships and History (Priority: P2)

As an authorized staff member, I want to maintain multiple memberships on a
profile and retain ended memberships so the member and staff can see their
membership history.

**Why this priority**: Memberships are part of the profile and must retain their
own identifiers and historical records.

**Independent Test**: Add multiple synthetic memberships, update one, and mark
one ended; verify their numbers, dates, and visibility.

**Acceptance Scenarios**:

1. **Given** a profile, **When** authorized staff add memberships, **Then** each membership has its own generated number, type, status, and start date, with an end date when supplied.
2. **Given** a membership without an end date, **When** staff save it with a start date, **Then** the membership can be saved.
3. **Given** a missing start date or an end date earlier than the start date, **When** staff attempt to save the membership, **Then** the invalid change is not saved and the issue is identified.
4. **Given** a membership is marked ended, **When** the member or authorized staff view the profile, **Then** the membership remains in its history with its number and dates.
5. **Given** the initial demo, **When** staff choose a membership type or status, **Then** the types `Demo type A` and `Demo type B` and statuses `Demo active` and `Demo ended` are identified as demo-only placeholders rather than production policy.

### User Story 8 - Request and Record Membership Changes (Priority: P2)

As a verified member, I want to request a membership change and see its
outcome, while staff apply changes only after review.

**Why this priority**: Membership changes need a visible review boundary rather
than direct member edits or implied approval.

**Independent Test**: Submit synthetic requests, record an approval and a
rejection as staff, and verify both the displayed outcomes and membership data.

**Acceptance Scenarios**:

1. **Given** a verified member and one of their memberships, **When** they submit the desired change, **Then** the request is recorded as submitted, the handoff is identified as simulated, and the membership is unchanged.
2. **Given** a submitted request, **When** authorized staff locate it, **Then** they can see the requested change and the simulated handoff state.
3. **Given** a submitted request and a simulated separate review, **When** authorized staff record an approval, **Then** the member can see the approval and the membership remains unchanged until staff apply the change.
4. **Given** an approved request, **When** authorized staff apply the approved change successfully, **Then** the membership is updated and the request is shown as applied.
5. **Given** a submitted request, **When** authorized staff record a rejection, **Then** the member can see the rejection and that request cannot be used to apply a membership change.
6. **Given** a simulated handoff fails, **When** the failure is reported, **Then** it does not imply that an external review or approval occurred.
7. **Given** an approved request whose membership update fails, **When** the request is viewed, **Then** it remains approved but not applied, the membership is unchanged, and the failure is reported.
8. **Given** another member's request, **When** a member attempts to view it, **Then** its details and outcome are not disclosed.

### Edge Cases

- An applicant provides a matching number or email but has no verified link:
  no existing profile is exposed.
- A proposed link conflicts with another verified account: the link is refused
  and staff must resolve the conflict.
- An enrollment is missing required information, or a membership has invalid
  dates: the invalid submission or change is not accepted.
- A simulated handoff fails: the failure is visible and must not imply that an
  external review or approval occurred. A failed membership update leaves the
  membership unchanged and is not shown as applied.
- A review-recording or audit operation fails: the failure is visible and must
  not be represented as a successful change.
- A member changes contact email: the sign-in email and existing profile
  association do not change as a consequence.
- A membership ends: its existing record, number, and dates remain visible as
  history.
- An account lacks staff entitlement or attempts another member's data: access
  is denied regardless of the path used to request the data.

## Requirements _(mandatory)_

### Functional Requirements

- **FR-001**: The demo MUST provide member and staff experiences within one application. Members MUST sign in using email one-time codes; invalid, expired, and previously used codes MUST NOT authenticate a member. Staff MUST use an organization-managed account with the designated staff entitlement.
- **FR-002**: The application MUST enforce access to enrollment, profile, membership, and request data for every operation. Members may access only their own submitted enrollment and, after verification, their own linked profile and related data. Staff access requires the designated staff entitlement.
- **FR-003**: A member MUST be able to submit an enrollment containing all five required fields: name, contact email, phone, date of birth, and mailing address. Until verification, the member MUST see only their own submitted enrollment details and MUST NOT edit profile data.
- **FR-004**: Authorized staff MUST be able to locate and read pending enrollments, record simulated verification, and either link an enrollment to an existing profile or create a new profile from it.
- **FR-005**: An email address, member number, or membership number MUST NOT by itself prove profile ownership. A conflicting link to a profile already associated with a different verified member account MUST NOT be created.
- **FR-006**: Authorized staff MUST be able to create, read, and update profiles and search by member number, membership number, name, or contact email. The application MUST generate a member number unique among profiles.
- **FR-007**: A verified member MUST be able to view their profile and related memberships and directly update only their contact email, phone, and mailing address. Staff manage profile name and date of birth.
- **FR-008**: Contact email and sign-in email MUST be separate. Updating a contact email MUST NOT change the sign-in email or the member's verified profile association.
- **FR-009**: Authorized staff MUST be able to maintain multiple memberships per profile. Each membership MUST have an application-generated number unique among memberships, a type, a status, and a required start date. An end date is optional and, when provided, MUST NOT precede the start date.
- **FR-010**: The demo MUST offer the placeholder membership types `Demo type A` and `Demo type B`, and the placeholder statuses `Demo active` and `Demo ended`. These values do not define production classifications or transition rules. Ended memberships MUST remain visible as history with their numbers and dates.
- **FR-011**: A verified member MUST be able to submit a request identifying one of their memberships and describing the desired change. Submitting the request MUST NOT directly change the membership.
- **FR-012**: The application MUST record membership requests and show the owning member their status and outcome. The demo's handoff to a separate review process MUST be identified as simulated and MUST NOT be represented as a real external integration or approval.
- **FR-013**: Authorized staff MUST be able to locate and read requests and record an approval or rejection from the simulated review. Only an approved request may be used to apply a membership change; staff MUST explicitly apply it, and the request is shown as applied only after the update succeeds. A rejected request MUST NOT be applied.
- **FR-014**: Every successful state-changing operation MUST produce an internal audit event recording an opaque actor reference, operation or changed field names, time, and outcome without profile values. Failed attempts MUST also be recorded when audit recording is available. A successful state change MUST NOT be committed without its required audit event.
- **FR-015**: Demo member records and test data MUST be synthetic and MUST NOT identify real people. Logs and traces MUST NOT contain personal information, account data, authentication codes, tokens, or request and response payloads. Test fixtures and AI prompts MUST NOT contain personal information. Data MUST be encrypted at rest and in transit, access to the data service MUST be private, and stored data MUST remain in approved US regions.
- **FR-016**: Failed operations MUST be reported without exposing member data or stack traces, and errors MUST provide a reference identifier. A failed simulated handoff MUST NOT be reported as a successful review. A failed membership update MUST leave the membership unchanged and MUST NOT be reported as applied.
- **FR-017**: Member and staff workflows MUST be operable by keyboard with visible focus. Fields and validation errors MUST have clear text available visually and to assistive technology, and errors MUST NOT rely on color alone. One-time-code entry MUST allow pasting.

### Non-Functional Requirements

- **NFR-001**: The demo MUST meet the constitution's p95 API latency target of
  under 300 ms at its expected peak workload. Before performance validation,
  the plan MUST record a product-owner-agreed expected workload and its basis.
  The plan MUST NOT invent a workload estimate.

### Key Entities

- **Member identity**: A signed-in member account with a sign-in email and, after staff verification, an association with its own profile.
- **Enrollment**: The five member-submitted profile fields and their pending or verified state, associated with the submitting account.
- **Member profile**: A generated member number, name, contact email, phone, date of birth, and mailing address.
- **Membership**: A generated membership number, type, status, start date, and optional end date, associated with a profile.
- **Membership change request**: A member's requested change to one membership, its simulated handoff, and its submitted, approved, rejected, or applied status.
- **Audit event**: An opaque actor reference, operation or changed field names, time, and outcome; it does not contain profile values.

## Success Criteria _(mandatory)_

### Measurable Outcomes

- **SC-001**: Reviewers can demonstrate all eight user stories with synthetic data, including both existing-profile and new-profile verification paths (FR-001 through FR-017).
- **SC-002**: In every specified pending, cross-member, and non-staff access scenario, no unauthorized enrollment, profile, membership, or request details are disclosed and no unauthorized change succeeds (FR-002 through FR-005, FR-007, FR-011).
- **SC-003**: In the specified approval, rejection, and failure scenarios, zero membership changes are applied from a pending or rejected request, and every successfully applied request follows a recorded approval (FR-011 through FR-013, FR-016).
- **SC-004**: In the specified multi-membership and invalid-date scenarios, each generated number is unique in its record category, invalid dates are rejected, and ended memberships remain visible with their numbers and dates (FR-006, FR-009, FR-010).
- **SC-005**: Every demonstrated state change has the required audit event. Inspection finds no profile values in audit events; no personal information, account data, authentication codes, tokens, or request and response payloads in logs or traces; and no personal information in test fixtures or AI prompts (FR-014, FR-015).
- **SC-006**: A reviewer can identify which member data is synthetic, which review handoffs are simulated, and which production decisions remain open, without mistaking the demo for a production-ready process (FR-012, FR-015).
- **SC-007**: Every specified member and staff workflow can be completed using a keyboard, field labels and errors are available to assistive technology, and one-time codes can be pasted (FR-017).
- **SC-008**: At the agreed peak demo workload, at least 95% of requests complete in under 300 ms (NFR-001).

## Assumptions

- The demo is limited to synthetic member records. The same application provides
  member and staff screens; it is the source of truth for demo profile and
  membership data.
- The member's sign-in email may initially match the contact email, but they
  are separate values and a contact update does not alter sign-in.
- Enrollment verification and membership-request handoffs are simulated for the
  demo. A simulated verification does not establish a production identity-proofing
  process. Staff record the simulated outcomes; they do not imply that a real
  external review system has been integrated.
- Demo-only membership types and statuses are placeholders. No production
  eligibility, classification, or status-transition policy is implied.
- The member identity provider and feasibility of the required email-code
  experience need security and design review. No provider or authentication
  bypass is approved by this specification.
- Security and privacy review must confirm that actor references support
  auditability without placing personal information or account data in logs or
  traces.
- The specified accessibility criteria are a baseline for the demo, not a
  claim of full conformance or certification. A broader conformance target
  remains subject to review.
- Expected peak load is not yet known and must be agreed and recorded in the
  implementation plan before performance validation (NFR-001). Availability,
  production retention and deletion, minor-member handling, production
  verification evidence, and the eventual review integration also remain open.
  This spec invents no load or performance result.
- **Out of scope**: real member data; production deployment or production
  identity proofing; a real ticketing or review integration; other systems
  consuming member data; production membership business rules; member-facing
  audit history; and non-US data residency.
- Product owner and tech lead review of this specification, plus applicable
  security and privacy reviews, remain necessary. This draft and the approved
  outline do not constitute approval to implement.
