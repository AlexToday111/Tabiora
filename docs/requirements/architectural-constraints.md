# Architectural Constraints

“Must” denotes a constraint. “Should” denotes a recommendation.

- **AC-001** — The system must be able to integrate with existing restaurant automation/POS systems where required.

- **AC-002** — Payment card processing should be delegated to a certified external payment provider rather than storing raw card details in the application.

- **AC-003** — The customer-facing functionality must be accessible from smartphones.

- **AC-004** — The architecture must support multiple restaurants as separate business entities.

- **AC-005** — The system must exchange booking, order and table-availability information with restaurant-side systems.

- **AC-006** — External integrations should be isolated from the core business logic so that different restaurant systems can be supported.

Technology and partner choices remain open in the [decision register](open-questions.md).

## Responsibility boundaries

| Party | Responsibility in the proposed model | Boundary |
| --- | --- | --- |
| Tabiora | Customer/staff interactions, authorization, coordinating booking and ordering, reflecting verified payment state, and tracking restaurant handoff | Does not infer availability, payment success, or delivery success from a sent request alone |
| Authoritative booking source | Decide whether one table/interval can be reserved and atomically create a booking | Tabiora or a restaurant/POS source: D-02 is open; there must be one coordinated authority for all booking channels |
| Restaurant/POS | Supply agreed restaurant-side information and receive confirmed reservations/pre-orders | Data ownership, bill source, acknowledgement, and local staff workflow remain D-02, D-05, D-06, D-10 |
| External payment provider | Process payment and provide a verifiable result/reference | Recommended by AC-002 and adopted as proposal P-PAY; no vendor, processing protocol, or credential design is selected |
| Staff notification endpoint | Receive a call notification for the relevant restaurant staff | Delivery evidence and offline/escalation policy remain D-07–D-08 |

AC-004 requires separate restaurant business entities. P-STAFF proposes restaurant-scoped access. Data isolation and any multi-country rollout remain design choices.

## Proposed integration contracts

| Logical port | Contract |
| --- | --- |
| BookingAuthority | Advisory preview and atomic create-if-available. [Outcomes and ownership](../architecture/adr/adr-001-authoritative-booking.md#decision) |
| POSIntegration | Handoff of confirmed reservations or eligible paid pre-orders, with acknowledgement or failure. Wider data synchronization remains open. |
| PaymentProviderPort | Initiate an attempt, verify results, and query an unresolved attempt. [Verification and handoff](../architecture/adr/adr-002-verified-payment-and-restaurant-handoff.md#decision) |
| StaffNotifier | Notify the relevant restaurant staff and report endpoint receipt or failure. [Waiter-call sequence](../architecture/sequence-diagram.md#waiter-call) |

An internal component or partner adapter can fulfil a port. See the [proposed deployment](../architecture/deployment-operational-view.md#proposed-architecture).

## Authority and failure handling

All booking channels must coordinate through the chosen authority to satisfy NFR-004 (D-02). P-INTEGRATION keeps handoff separate from booking and payment results and reuses the business reference on retries. D-06 governs acknowledgement, recovery limits, escalation, and compensation. See [failure handling and recovery](../architecture/deployment-operational-view.md#release-recovery-and-rollback).

## Planning scale and expansion boundary

[P-SCALE](../overview.md#target-businesses-and-planning-scale) is illustrative. Chain-wide access and cross-country data sharing remain unspecified. Check active restaurant membership and role under the [P-STAFF permission model](functional-requirements.md#proposed-staff-permissions).

The [expansion assessment](../architecture/README.md#geographical-expansion-assessment) lists the evidence needed for a future country rollout, including China as an example.
