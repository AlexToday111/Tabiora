# ASR and Traceability Matrix

## 1. ASR selection criteria

A requirement is treated as architecturally significant when at least one of the following holds:

1. It is an **approved invariant** that constrains system structure (e.g. NFR-004 no double booking, NFR-006 no cross-restaurant access).
2. It introduces an **external dependency** whose failure changes the customer or restaurant outcome (booking authority, payment provider, POS, staff endpoint).
3. Its failure mode has **High impact** in the Risk Register (double charge, double booking, cross-restaurant exposure, extended booking/payment outage).
4. It drives a **cross-cutting quality attribute** that cannot be retrofitted cheaply (performance, availability, observability, multi-restaurant isolation).

The selected ASR set below covers the six areas requested: concurrent booking, payment consistency, restaurant isolation, external integration/recovery, performance, and availability. Waiter-call delivery is included as a sub-case of external integration/recovery because its failure mode is High impact and it uses the same delivery-status pattern.

## 2. Use case catalogue (compact)

Use cases are referenced by stable IDs in the matrix. They are not new scope; each maps to recorded FRs.

| Use Case ID | Name | Primary FRs |
| --- | --- | --- |
| UC-01 | Search and filter restaurants | FR-001, FR-002 |
| UC-02 | View restaurant information, menu, and floor plan | FR-003, FR-004, FR-006 |
| UC-03 | Book a table for a selected interval | FR-005 |
| UC-04 | Select dishes and prepay a pre-order | FR-007, FR-008 |
| UC-05 | Hand off confirmed reservation/pre-order to the restaurant | FR-009 |
| UC-06 | Pay the restaurant bill from the phone | FR-011 |
| UC-07 | Staff view and manage reservations/pre-orders | FR-012, FR-013 |
| UC-08 | Staff receive, acknowledge, and resolve waiter calls | FR-014 |
| UC-09 | Customer calls a waiter from the phone | FR-010 |
| UC-10 | Onboard a second restaurant with isolated data | NFR-011, AC-004 |

## 3. Selected ASRs

| ASR ID | ASR statement | Why architecturally significant | Requirement status |
| --- | --- | --- | --- |
| ASR-01 | Concurrent overlapping requests for one table must produce at most one confirmed reservation across all booking channels. | Drives a single coordinated booking authority; failure is a High-impact customer-facing invariant breach. | NFR-004 **Approved invariant**; authority ownership **TBD** (D-02/Q-20). |
| ASR-02 | Payment outcomes must be truthful: timeout/late/duplicate results must not be reported as success or as rejection without verification; success must be applied once. | Drives PaymentProviderPort, reconciliation, and the UNKNOWN/REVIEW_REQUIRED states; failure mode is double charge or invisible charge. | FR-008/FR-011 baseline **Partial**; P-PAY **Proposed**; policy **TBD** (D-04). |
| ASR-03 | Staff operations must be authenticated, authorized, and scoped to the staff member’s authorized restaurant; no role crosses restaurant boundaries. | Drives the P-STAFF membership/role model and every management operation; failure is a High-impact cross-restaurant exposure. | NFR-006/AC-004 **Approved invariant**; role matrix/identity **TBD** (D-11/Q-33a–c). |
| ASR-04 | Confirmed reservation/pre-order information must reach the intended restaurant, with delivery failure tracked separately from booking/payment success and recoverable using the same business reference. | Drives POSIntegration port and DeliveryStatus; failure is High impact when the restaurant is not informed. | FR-009 baseline **Partial**; P-INTEGRATION **Proposed**; recovery limits **TBD** (D-06/Q-23). |
| ASR-05 | Customer-facing booking, search, menu, and waiter-call flows must be measurable at the Tabiora boundary with outcome-aware latency; numeric targets must not be claimed as approved until D-08 records them. | Drives measurement boundaries and prevents misleading performance claims; ties to NFR-001/002/009/010. | Measurement definitions **Agreed**; targets **TBD** (D-08/Q-39–Q-42); proposed evaluation targets only, not approved. |
| ASR-06 | Booking and payment operate 24/7 except scheduled maintenance; availability is measured separately from maintenance and provider outages; UNKNOWN payment is not success. | Drives probes and reporting; 24/7 ≠ 100 % uptime; failure mode is unsubstantiated availability claims or silent payment outage. | NFR-003 **Partial**; numerical target **TBD** (D-08/Q-43–Q-44). |
| ASR-07 | Waiter-call delivery distinguishes dispatch, endpoint receipt, and human acknowledgement; a customer-facing acceptance message must not claim a waiter has received a call from dispatch alone. | Drives StaffNotifier port and P-CALL three-state model; failure is High impact for in-restaurant service trust. | FR-010/FR-014 baseline **Partial**; P-CALL **Proposed**; recipient/escalation **TBD** (D-07/Q-34–Q-35, Q-42). |
| ASR-08 | Adding a second restaurant must not require core-code changes and must not allow cross-restaurant access. | Drives AC-004/NFR-011 and the P-STAFF scoping; failure is Medium–High impact for multi-restaurant architecture. | AC-004 **Approved invariant**; NFR-011 **Partial**; onboarding boundary **TBD** (D-01/D-02). |

## 4. Traceability matrix

Columns follow the requested chain: Business goal → Requirement(s) → Use Case → Architecture component / ADR → Test case → Risk. “Business goal” is a short label from §A.2 of the Project Context. Requirement status is noted in parentheses where relevant.

| ASR | Business goal | FR/NFR/AC | Use Case | Architecture component / ADR / proposal | Test case | Risk |
| --- | --- | --- | --- | --- | --- | --- |
| ASR-01 | Reduce booking uncertainty; booking integrity | NFR-004 (Approved invariant); FR-005 (Partial); AC-005 (Partial) | UC-03 | BookingAuthority port; ADR-001 authoritative booking; P-BOOK proposal; D-02 coordination mechanism (TBD) | Concurrent same-table conflict test per NFR-004 verification; cross-channel repeat using same authoritative source; reschedule against existing booking | RISK-001 (double booking / data-owner disagreement) |
| ASR-02 | Reduce payment friction before and during visit | FR-008, FR-011 (Partial); NFR-008 (Approved invariant for logging); AC-002 (recommended) | UC-04, UC-06 | PaymentProviderPort; ADR-002 verified payment and restaurant handoff; P-PAY proposal; D-04 policy (TBD) | Payment timeout, duplicate/out-of-order result, late-success-for-ineligible-order injection per recorded reliability checks; reconciliation test; UNKNOWN not counted as success | RISK-002 (UNKNOWN/late/duplicate payment) |
| ASR-03 | Protect restaurant data and staff operations | NFR-006, AC-004 (Approved invariants); FR-012–FR-014 (Partial) | UC-07, UC-08 | P-STAFF membership/role model (Proposed); restaurant-scoped authorization; D-11 role matrix (TBD) | Denial-test matrix: unauthenticated, unauthorized role, cross-restaurant, WAITER-only, MANAGER-only, both roles, different role set in another restaurant | RISK-004 (cross-restaurant access) |
| ASR-04 | Restaurant must receive confirmed information | FR-009 (Partial); AC-001, AC-005, AC-006 (Approved/recommended) | UC-05 | POSIntegration port; P-INTEGRATION DeliveryStatus; D-06 acknowledgement/recovery (TBD) | Restaurant-delivery failure injection after booking/payment success; retry with same business reference; failed delivery visible separately from domain status | RISK-003 (POS unavailable / undelivered handoff) |
| ASR-05 | Fast, predictable customer experience | NFR-001, NFR-002, NFR-009, NFR-010 (targets TBD); AC-003/NFR-007 (Partial) | UC-01, UC-02, UC-03, UC-09 | Measurement at Tabiora boundary; outcome-aware latency reporting; proposed evaluation targets only (not approved) | Outcome-aware p95/p99 latency tests per NFR verification definitions; invalid requests reported separately; external time included in end-to-end | RISK-005 (undefined NFR/workload/SLA) |
| ASR-06 | Booking and payment availability | NFR-003 (Partial); AC-002 (recommended) | UC-03, UC-04, UC-06 | Monitored booking/payment probes; availability reporting separate from maintenance and provider outages; D-08 targets (TBD) | Availability probes excluding approved maintenance; provider-outage injection; UNKNOWN payment retained as pending financial outcome, never success | RISK-005 (undefined availability target) |
| ASR-07 | Waiter call reaches staff | NFR-002 (target TBD); FR-010, FR-014 (Partial); D-07 (TBD) | UC-08, UC-09 | StaffNotifier port; P-CALL three-state model (Proposed); D-07 recipient policy (TBD) | Staff-endpoint failure injection: call retained with DELIVERY_FAILED; customer acceptance message does not claim human receipt; dispatch vs endpoint receipt vs acknowledgement recorded separately | RISK-009 (waiter-call delivery/acknowledgement) |
| ASR-08 | Multi-restaurant architecture | AC-004 (Approved invariant); NFR-011 (Partial); NFR-006 (Approved invariant) | UC-10 | Separate restaurant business entities; P-STAFF restaurant-scoped membership; D-01/D-02 onboarding boundary (TBD) | Second-restaurant onboarding with distinct staff/menu/table data; cross-restaurant operation attempt denied; count core-code vs configuration changes | RISK-004 (cross-restaurant access), RISK-007 (scope/timeline dependency) |

## 5. Notes on status, ownership, and monitoring

- **Approved vs Proposed vs TBD.** The “Approved invariant” label applies only where the recorded NFR/AC text uses “shall”/“must” and does not depend on an open D-ID. ASR-01, ASR-03, and the NFR-008 logging invariant are approved as properties; their *mechanisms* (authority ownership, role matrix, event list) are TBD. ASR-02, ASR-04, ASR-05, ASR-06, ASR-07, and ASR-08 depend on Proposed models or TBD policy and must not be presented as fully agreed.
- **Test cases are references, not new requirements.** Each test case maps to a verification method already recorded in `non-functional-requirements.md` or to a reliability check. No test case introduces a new acceptance threshold; thresholds remain TBD until D-08 (or the relevant D-ID) records them.
- **Proposed evaluation targets** (if used in any appendix) must be labelled “Proposed evaluation target — not approved” and must not appear in stakeholder commitments or as accepted NFRs.
- **Risks** refer to the Risk Register IDs (RISK-001–RISK-010). Residual risk for each ASR is High while its governing D-ID is Open and drops only when the decision record exists and the detection signal is being observed.
- **Full requirement registry** remains authoritative: FR-001–FR-027 in `functional-requirements.md`; NFR-001–NFR-012 and verification methods in `non-functional-requirements.md`; AC-001–AC-006 in `architectural-constraints.md`; D-01–D-13 and Q-01–Q-48 in `open-questions.md`. This matrix adds traceability and ASR rationale; it does not change any priority or scope.

## 6. Coverage check

| Required ASR area | Covered by |
| --- | --- |
| Concurrent booking / double booking | ASR-01 (NFR-004, FR-005, AC-005, D-02, ADR-001, RISK-001) |
| Payment consistency | ASR-02 (FR-008, FR-011, NFR-008, AC-002, D-04, ADR-002, RISK-002) |
| Restaurant isolation | ASR-03 (NFR-006, AC-004, P-STAFF, D-11, RISK-004) |
| External integration / recovery | ASR-04 (FR-009, AC-001/AC-005/AC-006, D-06, P-INTEGRATION, RISK-003); ASR-07 (waiter-call delivery, D-07, P-CALL, RISK-009) |
| Performance | ASR-05 (NFR-001/002/009/010, D-08, RISK-005) |
| Availability | ASR-06 (NFR-003, D-08, RISK-005) |
| Multi-restaurant architecture (supporting isolation and expansion) | ASR-08 (AC-004, NFR-011, D-01/D-02, RISK-004/RISK-007) |
