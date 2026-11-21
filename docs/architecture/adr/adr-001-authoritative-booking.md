# ADR-001 Use a Single Authoritative Booking Mechanism

## Status

**Proposed.** Booking authority ownership and the partner contract remain open under D-02. Reservation change and deadline rules remain open under D-03.

## Context / ASR

FR-004/FR-005 require availability and timed reservations. A preview can go stale before submission. NFR-004 requires preventing overlaps. The decision must cover all channels that can book a table, including applicable restaurant integrations (AC-004/AC-005). NFR-001 latency targets and workload remain D-08.

## Decision

Use one coordinated `BookingAuthority` for every channel that can commit a reservation for the same restaurant and table. `checkAvailability` provides an advisory preview. `createIfAvailable` checks for conflicts and creates the reservation as one atomic operation.

| Result | Meaning |
| --- | --- |
| `CONFIRMED` | The authority committed the reservation and returned its reference. The finite booking follows P-BOOK independently of checkout or restaurant handoff. |
| `CONFLICT` | An overlapping reservation prevented the requested commit. |
| `INVALID` | The authority rejected invalid input. |
| `SOURCE_UNAVAILABLE` | Tabiora cannot obtain an authoritative booking result. The operation may have committed if the reply was lost. Confirmation requires authoritative evidence. |

An authoritative preview may report table unavailability. Missing catalogue data follows UC-01.

If Tabiora owns booking, a relational transaction with database-enforced concurrency is a candidate. A Restaurant/POS authority needs an equivalent atomic contract. P-BOOK proposes half-open intervals. Time-zone and buffer policies remain D-02/D-03.

## Alternatives

- **Read availability, then insert without atomic protection.** Easy to prototype, but both requests can read free capacity and then confirm.
- **Allow Tabiora and POS to commit independently and synchronize later.** Each channel can continue locally, but reconciliation after the fact can discover two promises for the same table and interval.

## Consequences

- **Positive.** One contract enforces NFR-004, exposes conflict and outage outcomes, and isolates partner details behind a port.
- **Negative.** Availability and latency depend on the authority and any partner round trip. Every participating channel must use the contract. An ambiguous result after submission requires investigation at the authority before treating the booking as confirmed.

## Links / Verification

Test concurrent overlaps on one initially free table. At most one request confirms, with `CONFLICT` for losing valid requests when the source is healthy. Cover supported POS channels, invalid input, adjacent intervals, stale previews, outages, and a lost reply after a possible commit. An uncertain result yields `SOURCE_UNAVAILABLE` and requires authoritative evidence before confirmation. Use agreed D-08 workload and latency criteria.

[Original Lab 9 ADR-001](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.7qbrdid5xjwc#heading=h.tvb5yku6ecgt) · [Architecture Map](../architecture-map.md) · [Booking sequence](../sequence-diagram.md#table-reservation) · [UC-02](../../requirements/use-cases.md#uc-02--reserve-a-table) · [Reliability checks](../../requirements/non-functional-requirements.md#proposed-reliability-checks) · [Lab 11 Test Plan](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.r75t8oa0l1gw#heading=h.suljx3hzzq1j)
