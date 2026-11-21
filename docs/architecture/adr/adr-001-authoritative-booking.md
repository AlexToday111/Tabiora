# ADR-001 Use a Single Authoritative Booking Mechanism

## Status

**Proposed.** Booking authority ownership and the partner contract remain open under D-02. Reservation change and deadline rules remain open under D-03.

## Context / ASR

FR-004 and FR-005 require availability and time-specific reservations. NFR-004 requires that competing requests for the same table cannot confirm overlapping intervals. AC-004 and AC-005 extend the concern to distinct restaurants and restaurant-side channels. An available table shown to a customer can become occupied before the booking is submitted. NFR-001 still requires a response-time limit, with the target and workload open under D-08.

## Decision

Use one coordinated `BookingAuthority` for every channel that can commit a reservation for the same restaurant and table. `checkAvailability` provides an advisory preview. `createIfAvailable` checks for conflicts and creates the reservation as one atomic operation.

| Result | Meaning |
| --- | --- |
| `CONFIRMED` | The authority committed the reservation and returned its reference. The finite booking follows P-BOOK independently of checkout or restaurant handoff. |
| `CONFLICT` | An overlapping reservation prevented the requested commit. |
| `INVALID` | The authority rejected invalid input. |
| `SOURCE_UNAVAILABLE` | Tabiora cannot obtain an authoritative booking result. The operation may have committed if the reply was lost. Confirmation requires authoritative evidence. |

An authoritative preview can report that a table is unavailable for the requested interval. That is a business result. Missing restaurant or table catalogue data follows the discovery flow in UC-01.

If Tabiora owns booking, a relational transaction with database-enforced concurrency is a candidate. If Restaurant/POS owns booking, its adapter must rely on an equivalent atomic contract. Verify the chosen authority across participating channels before claiming conflict safety. P-BOOK proposes half-open intervals. Time-zone and buffer policies remain open under D-02/D-03.

## Alternatives

- **Read availability, then insert without atomic protection.** Easy to prototype, but both requests can read free capacity and then confirm.
- **Allow Tabiora and POS to commit independently and synchronize later.** Each channel can continue locally, but reconciliation after the fact can discover two promises for the same table and interval.

## Consequences

- **Positive.** One contract enforces NFR-004, exposes conflict and outage outcomes, and isolates partner details behind a port.
- **Negative.** Availability and latency depend on the authority and any partner round trip. Every participating channel must use the contract. An ambiguous result after submission requires investigation at the authority before treating the booking as confirmed.

## Links / Verification

Run simultaneous overlapping requests for one initially free table and verify at most one confirmed reference. With a healthy authority, competing valid requests that lose receive `CONFLICT`. Verify `INVALID` for invalid input. Cover adjacent intervals under P-BOOK, stale previews, authority outages, and a lost reply after a possible commit. Assert that an uncertain result returns `SOURCE_UNAVAILABLE` without optimistic confirmation. Repeat concurrency tests across Tabiora and POS if POS can take bookings. Use the agreed load and latency criteria when D-08 is resolved.

[Original Lab 9 ADR-001](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.7qbrdid5xjwc#heading=h.tvb5yku6ecgt) · [Architecture Map](../architecture-map.md) · [Booking sequence](../sequence-diagram.md#table-reservation) · [UC-02](../../requirements/use-cases.md#uc-02--reserve-a-table) · [Reliability checks](../../requirements/non-functional-requirements.md#proposed-reliability-checks) · [Lab 11 Test Plan](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.r75t8oa0l1gw#heading=h.suljx3hzzq1j)
