# Architecture and UML

**Proposed design.**

## Reading order

Start with the [Architecture Baseline v1.0](architecture-baseline.md) and [Architecture Map](architecture-map.md), then follow the decisions and UML views below. The [constraints](../requirements/architectural-constraints.md) explain integration boundaries. Open choices are in the [decision register](../requirements/open-questions.md).

## Architecture decisions and operations

| Document | Status and focus |
| --- | --- |
| [ADR-001 Single Authoritative Booking Mechanism](adr/adr-001-authoritative-booking.md) | Proposed atomic booking contract and competing-channel verification |
| [ADR-002 Verified Payment and Recoverable Restaurant Handoff](adr/adr-002-verified-payment-and-restaurant-handoff.md) | Proposed verified payment, eligibility, and acknowledged handoff |
| [Deployment & Operational View](deployment-operational-view.md) | Proposed physical boundaries, recovery, observability, and release gates |

## Diagram index

The Architecture Map and all 12 maintained UML views have editable sources and generated SVGs.

| View | Current diagram | Main traceability |
| --- | --- | --- |
| Logical architecture | [Architecture Map](architecture-map.md) | FR-001–FR-014, NFR-002, NFR-004–NFR-006, NFR-008, AC-001–AC-006, ADR-001, ADR-002 |
| Use cases | [General](../requirements/use-cases.md#general-use-case-diagram) | FR-001–FR-026; AC-001, AC-005 |
| Use cases | [Customer](../requirements/use-cases.md#customer-use-case-diagram) | FR-001–FR-011, FR-015–FR-026 |
| Use cases | [Waiter](../requirements/use-cases.md#waiter-use-case-diagram) | FR-013–FR-014; P-STAFF/P-CALL |
| Use cases | [Manager](../requirements/use-cases.md#manager-use-case-diagram) | FR-012–FR-013; NFR-006; P-STAFF |
| Classes | [General domain](class-diagram.md#general-domain-model) | Core visit entities; FR-017–FR-019 remain Should Have |
| Classes | [Booking/reservation](class-diagram.md#booking-and-reservation) | FR-004–FR-005, FR-009, FR-020; NFR-004 |
| Classes | [Ordering/payment](class-diagram.md#ordering-and-payment) | FR-006–FR-009; AC-002, AC-005–AC-006 |
| Classes | [Staff/operations](class-diagram.md#restaurant-staff-and-operations) | FR-010, FR-012–FR-014; NFR-006 |
| Sequence | [Table reservation](sequence-diagram.md#table-reservation) | UC-02; atomic booking invariant |
| Sequence | [Pre-order/checkout exit](sequence-diagram.md#pre-order-and-payment) | UC-03; confirmation, unpaid cancellation and abandonment |
| Sequence | [Payment outcomes](sequence-diagram.md#payment-initiation-and-outcomes) | UC-03; verified results, timeout, late success and review |
| Sequence | [Waiter call](sequence-diagram.md#waiter-call) | UC-04; routing, receipt, acknowledgement, resolution |

[Source and SVG file index](../../assets/diagrams/source/README.md#files)

## Scope and modelling conventions

The [functional requirements](../requirements/functional-requirements.md) define scope, priorities, [proposed states](../requirements/functional-requirements.md#proposed-lifecycle-model), and [staff permissions](../requirements/functional-requirements.md#proposed-staff-permissions). The [deployment view](deployment-operational-view.md) groups logical modules in one proposed backend. Bill payment (FR-011) and changes (FR-020/FR-021) have [use cases](../requirements/use-cases.md) and await detailed integration sequences.

## Geographical expansion assessment

P-SCALE assumes a [single-region pilot](../overview.md#target-businesses-and-planning-scale). China is an example for assessing a future rollout.

| Software concern | Consequence to investigate before adding a country | Evidence needed; no decision implied |
| --- | --- | --- |
| Language and locale | Separate translatable text and localized menu content; support appropriate date, number, currency, address, and phone formats | Reviewed translations and country-specific task walkthroughs |
| Restaurant time zones | Represent booking intervals as unambiguous instants with restaurant zone context; show restaurant-local visit times and handle clock changes | Cross-zone booking, midnight, and clock-change tests; exact time model proposed under D-02 |
| Data governance | Determine applicable privacy, residency, transfer, retention, and access obligations for the planned data flows | Country-specific assessment before choosing storage/processing locations; no legal conclusion or compliance claim is made here |
| Network and placement | Measure customer/staff-to-system and system-to-provider/POS latency from candidate regions, including mainland China if considered | Regional measurements and failure tests before deciding whether additional infrastructure is justified |
| Regional payments | Verify currencies, payment methods, provider availability, result verification, and reconciliation semantics | Provider contract tests; retain the PaymentProviderPort boundary |
| Restaurant integrations | Identify differences in POS availability ownership, bill data, menu formats, and acknowledgement behavior | Adapter/contract tests and a booking-authority agreement for all channels |
| Capacity and deployment | Recalculate restaurant/customer counts, peak request mix, regional dependencies, rollout and rollback needs | A revised workload envelope and deployment options; no automatic move to microservices or a region per country |
| Monitoring and recovery | Observe regional latency, errors, delivery failures, payment UNKNOWN/review queues, and cross-region dependencies | Regional dashboards/probes and agreed recovery ownership/targets |

Before expanding, revisit D-01, D-02, D-05, D-08, and P-SCALE using the evidence above. Partner availability and software capacity require separate checks.

## Superseded diagrams and maintenance

The focused views replace the earlier combined class and sequence models. The [source guide](../../assets/diagrams/source/README.md#files) maps maintained sources to exports and gives the validation workflow.

[Back to project documentation](../../README.md#documentation)
