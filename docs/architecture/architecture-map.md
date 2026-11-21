# Tabiora Architecture Map

**Proposed logical design.** Read the map from the Customer App and Staff Interface through the Application API and core modules to the integration ports and external systems. The pilot proposes one backend deployable for the API, modules, and adapters. [Deployment & Operational View](deployment-operational-view.md#why-a-modular-monolith-for-the-pilot) · [Editable Lab 10 map](https://drive.google.com/file/d/1uazruv4TbqWrvL1MU_owLgO34Xjf3RWy/view)

![Tabiora Architecture Map showing the Tabiora boundary, logical modules, external systems, and legend](../../assets/diagrams/dineflow-architecture-map.svg)

[Editable draw.io source](../../assets/diagrams/source/dineflow-architecture-map.drawio) · [Full-size SVG](../../assets/diagrams/dineflow-architecture-map.svg)

For print, place the SVG at full content width on a landscape page to retain readable labels and line patterns.

Solid arrows show calls and integration requests. Payment success requires provider verification, restaurant delivery requires acknowledgement, and staff notification receipt is separate from waiter acknowledgement. Dashed arrows show candidate recovery invoking the existing payment and handoff logic. Dotted arrows show access to proposed persistence.

The interfaces cover smartphone access (AC-003) and restaurant-scoped staff operations (FR-012–FR-014, NFR-006, AC-004). Discovery and menus cover FR-001–FR-004 and FR-006. Important operation records support NFR-008. See the table below for integration and lifecycle traceability.

## State ownership

| State or record | Owner and boundary | Traceability |
| --- | --- | --- |
| Reservation | One coordinated authority determines whether a table and interval can be committed. Tabiora or Restaurant/POS may fulfil `BookingAuthority` under D-02. An overlap returns `CONFLICT`, invalid input returns `INVALID`, and an unobtainable authoritative result returns `SOURCE_UNAVAILABLE`. Only authoritative evidence establishes `CONFIRMED`. | FR-004–FR-005, NFR-004, AC-001, AC-004–AC-005, [ADR-001](adr/adr-001-authoritative-booking.md) |
| `Payment` and `PreOrder` | The Payment Provider is the source of verifiable financial outcomes. Tabiora tracks payment attempts and evaluates pre-order and visit eligibility. The restaurant bill follows UC-06 and its source remains open under D-05. | FR-007–FR-008, FR-011, NFR-005, NFR-008, AC-002, [ADR-002](adr/adr-002-verified-payment-and-restaurant-handoff.md) |
| `DeliveryStatus` | Tabiora tracks restaurant handoff separately. Agreed Restaurant/POS acknowledgement establishes `DELIVERED`. D-06 governs acknowledgement and recovery. | FR-009, AC-005–AC-006, P-INTEGRATION, [ADR-002](adr/adr-002-verified-payment-and-restaurant-handoff.md) |
| `WaiterCall` | Tabiora retains the call. Endpoint receipt establishes `NOTIFIED` under P-CALL. An authorized waiter in that restaurant establishes `ACKNOWLEDGED`. Routing and escalation remain open under D-07. | FR-010, FR-014, NFR-002, NFR-006, AC-004 |

Relational persistence is proposed for Tabiora-owned records. If Tabiora owns booking under D-02, the authority can use the proposed store to commit bookings. Restaurant handoff starts from confirmed reservations or eligible paid pre-orders. The candidate outbox and retry processing reuse stable references for handoff and payment reconciliation. Partner capabilities, acknowledgement behavior, and availability require verification. [Architecture Baseline v1.0](architecture-baseline.md) · [Proposed lifecycle model](../requirements/functional-requirements.md#proposed-lifecycle-model)
