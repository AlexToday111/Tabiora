# Development Approach and Methodology

**Planned approach.** Four third-year developers use one Scrum Product Backlog and one Sprint cycle. Rapid Prototyping validates uncertain interactions inside that cycle; validated backlog items can then become implementation increments. The proposed Sprint length is two weeks. [Ernest Kudakaev](https://github.com/AlexToday111) serves as both Product Owner and Scrum Master. The team shares testing. This is a plan, not a claim that Sprints or stakeholder interviews have already occurred.

## Why this approach fits Tabiora

A small university team can test floor-plan selection, checkout, and waiter calls with representative users inside short Sprints. Rapid Prototyping lets the team review booking authority, payment recovery, and staff permissions before committing integration effort.

| Selection factor | Tabiora-specific reason | Process consequence |
| --- | --- | --- |
| Product type | Customers, waiters, and managers use different flows; floor-plan booking and checkout have visible failure paths. | Review each role's prototype and acceptance cases in the same Sprint. |
| Project scale | The first release is a proposed restaurant pilot; restaurant count, traffic, and geography remain open under D-01/D-08. | Deliver a small, reviewable increment and revise scope after evidence rather than assuming a large deployment. |
| Team size | Four developers share implementation and testing; Ernest combines Product Owner and Scrum Master accountabilities. | Use one backlog and short coordination meetings, with an explicit owner for ordering work. |
| Team competence | Third-year students can validate unfamiliar payment, POS, and concurrency behavior before committing to integration details. | Put uncertain flows into prototype tasks and pair them with failure-focused test design. |
| Business factors | Restaurant rules, partner contracts, and stakeholder availability are not yet fixed; the team also works within a university schedule. | Time-box refinement, record D-IDs, and seek stakeholder decisions at Review before treating rules as accepted. |
| Required artifacts | The final architecture work needs traceable requirements, use cases, diagrams, decisions, test cases, and mock process records. | Link backlog items and review evidence to FR/NFR/UC and D-IDs; retain the same records across Sprints. |
| Technological factors | Booking authority may be local or in Restaurant/POS; payment and notification outcomes can arrive late or fail. | Prototype user-facing states first, then implement adapters and recovery paths as testable increments. |

## Planned prototype validation

| Prototype | Validation task | Evidence and decision |
| --- | --- | --- |
| Discovery and booking | Ask a customer to find a restaurant and select a table/time; simulate two competing booking requests | Observe task completion and comprehension of conflict messages; review P-BOOK and authority decision D-02 |
| Pre-order checkout | Select dishes, review the amount, then simulate confirmation, rejection, timeout, cancellation before payment, and abandoned checkout | Verify that no unknown payment is presented as failed or paid; review P-PAY with stakeholders |
| Staff service queue | Ask a waiter to acknowledge and resolve a call; simulate failed notification delivery and unauthorized access | Validate role scope, call state labels, and escalation policy (P-STAFF, P-CALL) |

The prototype tasks focus on the clarity of customer and staff flows. The [Implementation and Testing Plan](implementation-testing.md) describes checks for payment/POS integrations and methods for measuring quality targets.

## Example Sprint Goals

| Candidate Sprint | Goal | Reviewable result and acceptance examples |
| --- | --- | --- |
| Booking | Validate table selection and conflict handling | Clickable prototype, UC-02 walkthrough, and concurrent-request test design. Given one table/interval and two requests, at most one succeeds; the other receives a conflict (FR-005, NFR-004). |
| Pre-order and payment | Validate honest payment status and restaurant delivery | UC-03 prototype with provider/POS simulations. An unanswered provider request stays UNKNOWN; a duplicate success does not create a second payment or order; delivery failure does not erase payment success. |
| Restaurant service | Validate scoped waiter and manager roles, including one employee holding both | UC-04/UC-05 prototype and permission cases. An unrelated restaurant's staff cannot access its requests; an acknowledgement is distinguishable from resolution. Proposed permissions must be approved before becoming release acceptance criteria. |

Quantitative acceptance depends on agreed workloads and targets (D-08).

## One Sprint and prototype cycle

Use one Product Backlog and one Sprint Goal and Sprint Backlog per proposed two-week Sprint. A focused prototype can fit one Sprint. Larger workflows may span several. The illustrative ten-day schedule overlaps activities.

| Stage | Timing | Participants | Deliverable and completion condition |
| --- | --- | --- | --- |
| Sprint Planning | Day 1, about 2 hours | Four developers, including Ernest as Product Owner and Scrum Master | One Sprint Goal and selected, estimated backlog items with draft acceptance cases; unresolved business rules retain D-IDs. |
| Requirements refinement | Days 1–2, two 60–90 minute sessions plus follow-up | Team and available customer/staff representative | Questions are linked to FR/NFR and UC IDs; proposed answers, example inputs, failure paths, and unanswered decisions are recorded before prototype work starts. |
| Prototype design | Days 2–3, about 2 days | Developers; team member responsible for testing reviews failure and permission cases | Reviewable floor-plan, checkout, or staff flow with a task script. Mark simulated provider/POS responses. |
| Implementation | Days 4–7, about 4 days | Four developers sharing testing | Demonstrable prototype or implementable increment; each changed behavior has linked acceptance cases and disclosed mocks. |
| Validation | Days 7–9, about 2 days overlapping implementation | Developers sharing testing and available users | Task observations and conflict, failure, and abandonment results are recorded against the script; failed or unobserved cases stay open. |
| Sprint Review | Day 10, about 1 hour | Team and available stakeholders | Demonstrate the Sprint Goal and record accepted feedback separately from decisions that still need an owner. |
| Backlog refinement | Day 10, about 1 hour after review | Team; Ernest orders the Product Backlog as Product Owner | Split each validated scenario into independently testable UI, domain, integration, and test tasks as needed; update priority, dependencies, and acceptance cases. |
| Retrospective | Day 10, about 45 minutes | Whole team | Record one or two process changes and who will check them next Sprint. |

Use a short Daily Scrum to coordinate. End the Sprint with a reviewable artifact, acceptance evidence, updated requirements and decisions, and a revised backlog.

The requirements meeting is a time-boxed part of refinement, not a separate development process. The Sprint Review is the single point where prototype observations feed the same Product Backlog. A prototype result does not by itself approve a business rule: the relevant stakeholder must confirm it, or the D-ID remains open. Only ready, scoped items are selected for the next implementation increment.

## Mock / Simulated Process Records

The records below show how the team would document a requirements discussion and the feedback from a Sprint Review. They are illustrative examples for the proposed process.

### Mock requirements meeting — illustrative 29 October 2026, Day 2 of a mid-semester Sprint

| Field | Simulated record |
| --- | --- |
| Duration and roles | 75 minutes; four developers (one acting as Product Owner and Scrum Master and one QA engineer), restaurant manager representative, and customer representative. |
| Goal and source | Refine UC-02/UC-03, FR-005/FR-007–FR-009 and NFR-004 for a floor-plan booking and checkout prototype. |
| Questions asked | Which system owns table availability across walk-ins and online bookings (Q-20/D-02)? When can a visit be cancelled (Q-28/D-03)? How long may an unpaid draft remain (Q-29/D-04)? What confirms restaurant receipt (Q-23/D-06)? |
| Simulated outcome | Team drafts separate `CONFLICT`, `INVALID`, and `SOURCE_UNAVAILABLE` booking examples and a payment-timeout example. No partner or stakeholder answer is claimed. |
| Follow-up and evidence | Owner of D-02/D-03/D-04/D-06 must obtain decisions; the developer responsible for testing drafts concurrent-booking and timeout cases; developers prepare a labelled mock authority/provider interaction. Record the question log and revised acceptance cases before Sprint Review. |

### Mock Sprint Review — illustrative 10 November 2026, Day 10 of the same mid-semester Sprint

| Field | Simulated record |
| --- | --- |
| Duration and roles | 60 minutes; team and available product, restaurant staff, and customer representatives. |
| Demonstration | Walk through restaurant search, table selection and a simulated concurrent booking; show checkout with a provider timeout and a separate restaurant-delivery status. All integrations are explicitly labelled as mocks. |
| Simulated observation | The scripted conflict path shows one `CONFIRMED` booking and one `CONFLICT`; the timeout path shows `UNKNOWN` and no paid confirmation. This describes the intended review script, not an observed test run. |
| Open decisions | Booking authority across all channels (D-02), checkout expiry/review owner (D-04), POS acknowledgement (D-06), and numerical targets (D-08) stay open. No production readiness is inferred. |
| Backlog update | Split the candidate booking item into availability UI, atomic `BookingAuthority` contract, conflict/error presentation, and concurrency test tasks. Split payment into attempt persistence, verified-result handling, same-reference reconciliation, and timeout/duplicate tests. Product ordering and acceptance remain pending. |

## From prototype evidence to implementation work

For waiter-call testing, record the context against UC-04/UC-05, FR-014, NFR-006, and D-11. Test WAITER-only, MANAGER-only, both roles in one restaurant, and different assignments elsewhere. Keep P-STAFF proposed pending approval. Update backlog items for scoped authorization, acknowledgement, and denial tests with criteria and evidence. Select ready items in Sprint Planning and re-test in the increment.

For abandoned checkout, update UC-03/P-PAY and add expiry and reconciliation tests. Keep the deadline open under Q-29/D-04. Prototype evidence informs backlog ordering. Recorded MoSCoW priorities and service targets require their own decisions.

## Deliverables and constraints

The repository contains the specification, models, and decision register. Prototypes, feedback, backlogs, and test evidence are planned. The implementation stack and delivery tooling remain open. [Implementation and Testing Plan](implementation-testing.md) · [Deployment and release gates](../architecture/deployment-operational-view.md#staging-release-gates). Scrum terminology follows [The Scrum Guide (2020)](https://drive.google.com/file/d/1jWeLbegvTP-cHNotmto2tx4bhxrGMC9h/view?usp=drivesdk) from the course document library.
