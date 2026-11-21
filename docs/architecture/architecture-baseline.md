# Architecture Baseline v1.0

Status **Design-stage team reference**. The recorded requirements define Tabiora's scope. The P-* policies below describe the proposed logical design. The decision register records open questions and the evidence needed to close them.

## System boundary and sources

Tabiora provides a smartphone-accessible Customer App and a Staff Interface. It coordinates discovery, booking, pre-orders and payments, waiter calls, and handoff to the restaurant. The external Payment Provider processes payments and supplies verifiable outcomes. Restaurant/POS systems exchange agreed restaurant data and receive confirmed information. A staff notification endpoint can provide receipt evidence. The restaurant remains responsible for seating, preparation, and service.

Use the existing [FR priorities and acceptance checks](../requirements/functional-requirements.md), [NFRs](../requirements/non-functional-requirements.md), [ACs](../requirements/architectural-constraints.md), and [UCs](../requirements/use-cases.md) when reviewing changes. Use [P-IDs for proposals and D-IDs for decision areas](../requirements/open-questions.md). FR-001–FR-014 form the candidate MVP, including the distinct FR-011 bill-payment workflow. Simulated or partial prototype workflows leave those release priorities intact.

The pilot proposal groups the Application API, logical modules, and port adapters in one modular backend deployable. [Deployment rationale and trade-offs](deployment-operational-view.md#why-a-modular-monolith-for-the-pilot)

## Invariants and lifecycle vocabulary

| Invariant | Traceability |
| --- | --- |
| At most one reservation can be confirmed for overlapping intervals on the same restaurant table. Under P-BOOK, confirmation is atomic and the finite reservation runs independently of checkout. | FR-004, FR-005, NFR-004, AC-004–AC-005, UC-02 |
| `CONFLICT` means an overlap prevented the commit. `INVALID` means the input failed validation. `SOURCE_UNAVAILABLE` means Tabiora cannot obtain an authoritative result. An uncertain result leaves confirmation unestablished. | FR-005, NFR-004, UC-02, [ADR-001](adr/adr-001-authoritative-booking.md) |
| Only a verified provider result establishes payment success. A timeout or missing verified result yields `UNKNOWN` under P-PAY and requires reconciliation of the existing attempt. | FR-008, NFR-005, NFR-008, AC-002, UC-03 |
| `DELIVERED` requires agreed restaurant acknowledgement. Handoff remains separate from the reservation and payment outcomes. | FR-009, AC-005–AC-006, P-INTEGRATION |
| Each staff action checks the role in the relevant restaurant membership. Under P-STAFF, `WAITER` and `MANAGER` are independently assignable. Under P-CALL, endpoint receipt and human acknowledgement are distinct. | FR-010, FR-012–FR-014, NFR-002, NFR-006, AC-004, UC-04–UC-05 |

The [proposed lifecycle model](../requirements/functional-requirements.md#proposed-lifecycle-model) owns the state names and transitions for `Reservation` (P-BOOK), `PreOrder` and `Payment` (P-PAY), `WaiterCall` (P-CALL), and `DeliveryStatus` (P-INTEGRATION). The [staff permission matrix](../requirements/functional-requirements.md#proposed-staff-permissions) owns the proposed role allocation.

## Open dependencies

| ID | Open decision |
| --- | --- |
| D-02 | Booking ownership, all-channel coordination, and POS contracts |
| D-03 | Reservation changes, cancellation, and deadlines |
| D-04 | Checkout expiry, payment and refund policy, and review ownership |
| D-05 | Bill source, pricing adjustments, and settlement |
| D-06 | Restaurant acknowledgement, retry limits, and escalation |
| D-07 | Visit context, waiter routing, and no-response handling |
| D-08 | Workload, quality targets, and measurement conditions |

Update this baseline when a revision changes a contract or invariant. Record actual approvals and affected requirements in the [decision register](../requirements/open-questions.md#decision-completion-rule).
