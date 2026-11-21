# Architecture Baseline v1.0

**Design-stage team reference.**

## System boundary and sources

Tabiora coordinates discovery, booking, pre-orders, payments, and waiter calls through the Customer App and Staff Interface. The Payment Provider supplies verifiable financial outcomes, the Restaurant/POS exchanges agreed information, and the staff endpoint provides notification receipt evidence. The restaurant handles seating, preparation, and service. [Architecture Map](architecture-map.md)

FR-001–FR-014 define the candidate MVP, including separate bill payment under FR-011. Prototype scope leaves the recorded priorities intact. [FRs and acceptance checks](../requirements/functional-requirements.md) · [NFRs](../requirements/non-functional-requirements.md) · [ACs](../requirements/architectural-constraints.md) · [Use cases](../requirements/use-cases.md) · [P-/D-IDs](../requirements/open-questions.md)

The pilot proposes one modular backend deployable. [Deployment rationale](deployment-operational-view.md#why-a-modular-monolith-for-the-pilot)

## Invariants and lifecycle vocabulary

| Invariant | Traceability |
| --- | --- |
| At most one reservation can be confirmed for overlapping intervals on the same restaurant table. Under P-BOOK, confirmation is atomic and the finite reservation runs independently of checkout. | FR-004, FR-005, NFR-004, AC-004–AC-005, UC-02 |
| `CONFLICT` means an overlap prevented the commit. `INVALID` means the input failed validation. `SOURCE_UNAVAILABLE` means Tabiora cannot obtain an authoritative result. An uncertain result leaves confirmation unestablished. | FR-005, NFR-004, UC-02, [ADR-001](adr/adr-001-authoritative-booking.md) |
| Only a verified provider result establishes payment success. A timeout or missing verified result yields `UNKNOWN` under P-PAY and requires reconciliation of the existing attempt. | FR-008, NFR-005, NFR-008, AC-002, UC-03 |
| `DELIVERED` requires agreed restaurant acknowledgement. Handoff remains separate from the reservation and payment outcomes. | FR-009, AC-005–AC-006, P-INTEGRATION |
| Each staff action checks the role in the relevant restaurant membership. Under P-STAFF, `WAITER` and `MANAGER` are independently assignable. Under P-CALL, endpoint receipt and human acknowledgement are distinct. | FR-010, FR-012–FR-014, NFR-002, NFR-006, AC-004, UC-04–UC-05 |

Canonical [states and transitions](../requirements/functional-requirements.md#proposed-lifecycle-model) and [staff permissions](../requirements/functional-requirements.md#proposed-staff-permissions) are in the functional requirements.

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

Update the baseline when a contract or invariant changes. Record approvals and affected requirements in the [decision register](../requirements/open-questions.md#decision-completion-rule).
