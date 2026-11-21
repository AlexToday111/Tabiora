# Development Approach and Methodology

**Planned approach.** Three third-year developers and one tester use iterative prototyping and incremental development within Scrum. Product Owner and Scrum Master accountabilities remain unassigned. The team shares testing.

## Why this approach fits Tabiora

A small university team can test floor-plan selection, checkout, and waiter calls with representative users inside short Sprints. Rapid Prototyping lets the team review booking authority, payment recovery, and staff permissions before committing integration effort.

## Planned prototype validation

| Prototype | Validation task | Evidence and decision |
| --- | --- | --- |
| Discovery and booking | Ask a customer to find a restaurant and select a table/time; simulate two competing booking requests | Observe task completion and comprehension of conflict messages; review P-BOOK and authority decision D-02 |
| Pre-order checkout | Select dishes, review the amount, then simulate confirmation, rejection, timeout, cancellation before payment, and abandoned checkout | Verify that no unknown payment is presented as failed or paid; review P-PAY with stakeholders |
| Staff service queue | Ask a waiter to acknowledge and resolve a call; simulate failed notification delivery and unauthorized access | Validate role scope, call state labels, and escalation policy (P-STAFF, P-CALL) |

Label simulated payment and POS behavior. Use it as interaction evidence. Partner contracts and numerical targets require separate verification.

## Example Sprint Goals

| Candidate Sprint | Goal | Reviewable result and acceptance examples |
| --- | --- | --- |
| Booking | Validate table selection and conflict handling | Clickable prototype, UC-02 walkthrough, and concurrent-request test design. Given one table/interval and two requests, at most one succeeds; the other receives a conflict (FR-005, NFR-004). |
| Pre-order and payment | Validate honest payment status and restaurant delivery | UC-03 prototype with provider/POS simulations. An unanswered provider request stays UNKNOWN; a duplicate success does not create a second payment or order; delivery failure does not erase payment success. |
| Restaurant service | Validate scoped waiter and manager roles, including one employee holding both | UC-04/UC-05 prototype and permission cases. An unrelated restaurant's staff cannot access its requests; an acknowledgement is distinguishable from resolution. Proposed permissions must be approved before becoming release acceptance criteria. |

Quantitative acceptance depends on agreed workloads and targets (D-08).

## One Sprint and prototype cycle

Use one Product Backlog and one Sprint Goal and Sprint Backlog per proposed two-week Sprint. A focused prototype can fit one Sprint. Larger workflows may span several. The illustrative ten-day schedule overlaps activities.

| Stage | Timing | Participants | Deliverable |
| --- | --- | --- | --- |
| Sprint Planning | Day 1, about 2 hours | Three developers and tester, product participation TBD | Goal, capacity, linked backlog items, initial acceptance cases |
| Requirements refinement | Days 1–2, two 60–90 minute sessions plus follow-up | Team and available customer/staff representative | Refined use cases, Q/D-IDs, proposed criteria |
| Prototype design | Days 2–3, about 2 days | Developers, tester reviews failure and permission cases | Flow, screens, or executable model |
| Implementation | Days 4–7, about 4 days | Developers and tester | Demonstrable prototype or increment, with mocks disclosed |
| Validation | Days 7–9, about 2 days overlapping implementation | Tester, developers, and available users | Task observations and conflict, failure, and abandonment evidence |
| Sprint Review | Day 10, about 1 hour | Team and available stakeholders | Evidence against the goal, feedback, separately recorded decisions |
| Backlog refinement | Day 10, about 1 hour after review | Team, product ordering responsibility TBD | Revised items, criteria, dependencies, and priorities |
| Retrospective | Day 10, about 45 minutes | Whole team | One or two improvements for the next Sprint |

Use a short Daily Scrum to coordinate. End the Sprint with a reviewable artifact, acceptance evidence, updated requirements and decisions, and a revised backlog.

## From prototype evidence to implementation work

For waiter-call testing, record the context against UC-04/UC-05, FR-014, NFR-006, and D-11. Test WAITER-only, MANAGER-only, both roles in one restaurant, and different assignments elsewhere. Keep P-STAFF proposed pending approval. Update backlog items for scoped authorization, acknowledgement, and denial tests with criteria and evidence. Select ready items in Sprint Planning and re-test in the increment.

For abandoned checkout, update UC-03/P-PAY and add expiry and reconciliation tests. Keep the deadline open under Q-29/D-04. Prototype evidence informs backlog ordering. Recorded MoSCoW priorities and service targets require their own decisions.

## Proposed Definition of Done

For a documentation/prototype item:

- Related requirements, main flow, and failure behavior are traceable.
- Proposed rules and unresolved decisions are labelled; no stakeholder approval is inferred from a demonstration.
- A peer has reviewed consistency across prose, use cases, class models, and sequences.
- UML sources render, PNGs have been visually inspected, and local links resolve.
- Prototype acceptance cases have results recorded, including conflicts and access failures where relevant.

For a future implementation item, additionally require reviewed code, meaningful behavior/security checks, relevant integration evidence, and verified recovery behavior. Simulated integrations and unagreed performance thresholds must be reported as limitations. This Definition of Done is proposed and has not yet been adopted by the team.

## Deliverables and constraints

The repository contains the specification, models, and decision register. Prototypes, feedback, backlogs, and test evidence are planned. The implementation stack and delivery tooling remain open. [Deployment and release gates](../architecture/deployment-operational-view.md#staging-release-gates)
