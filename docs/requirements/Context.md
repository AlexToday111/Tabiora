# A. Project Context

## A.1 Problem and solution boundary

Restaurant visits involve avoidable friction: uncertain table availability, waiting time, difficulty contacting staff, and delays in ordering or payment. Tabiora is intended to improve the customer journey before, during, and after a visit by bringing restaurant discovery, reservations, pre-ordering, service calls, and bill payment into one experience, while supporting restaurant staff in coordinating customer requests.

The boundary is **coordination and truthful state reporting, not assumption**. Tabiora is responsible for customer and staff interactions, authorization, coordinating booking and ordering, reflecting verified payment state, and tracking restaurant handoff. It must not infer table availability, payment success, or restaurant delivery success from a sent request.

Within that boundary, the following are **approved invariants** (from the recorded NFR/AC text) and not negotiable proposals:

- No two overlapping confirmed reservations for the same table (NFR-004).
- No unencrypted transport of sensitive customer or payment data (NFR-005).
- No unauthenticated, unauthorized, or cross-restaurant management access (NFR-006, AC-004).
- Important booking, order, and payment operations are logged (NFR-008).
- Card processing is delegated to a certified provider; raw card details are not stored (AC-002, recommended constraint).

Everything about *how* those invariants are achieved — authoritative booking source, payment lifecycle, handoff acknowledgement, call routing, staff roles — is currently **proposed** (P-BOOK, P-PAY, P-INTEGRATION, P-CALL, P-STAFF) or **open** (D-01–D-13).

## A.2 Business goals and product purpose

Recorded business goals:

- Reduce avoidable visit friction by allowing customers to search, reserve, pre-order, prepay, call a waiter, and pay a bill digitally (FR-001–FR-014).
- Support restaurant discovery and filtering by dishes, price, distance, and other supported parameters (FR-001–FR-002; additional parameters remain Open — D-01/Q-14).
- Provide restaurant information, menu, and an interactive floor plan with table locations and availability (FR-003, FR-004, FR-006; content ownership Open — D-10/Q-21).
- Support time-based reservations and pre-orders with explicit handling of conflicts, invalid inputs, payment outcomes, and handoff results (FR-005, FR-007–FR-009; D-02–D-06).
- Support in-restaurant service through waiter calls and digital bill payment (FR-010, FR-011, FR-014; D-05, D-07).
- Support staff in viewing and managing incoming reservations and pre-orders (FR-012–FR-013; D-11/Q-33a–c).
- Optionally use activity and preferences for personalization, subject to privacy and data-handling constraints (FR-017–FR-019; D-09/Q-47).

The product purpose is **not** to guarantee reduced waiting times or automatic operational success. These are intended outcomes. No latency, availability, conversion, or satisfaction target is approved; measurable success criteria remain TBD under D-08, with Proposed evaluation targets recorded separately (see the Measurable Quality Attributes document). Questions about accounting, refunds, or additional capabilities do not add them to release scope.

## A.3 Stakeholders

| Stakeholder | Interest / need | Boundary in the recorded baseline |
| --- | --- | --- |
| Customer | Discover restaurants, view information/menu/floor plan, reserve a table, pre-order, prepay, call a waiter, pay the bill from a smartphone. | Customer operations scoped to the customer’s own visit in the prototype. |
| Waiter | Receive and act on waiter-call notifications; view relevant pre-orders where permitted. | WAITER role scoped to authorized restaurant/service context; pre-order *management* not granted in the proposed baseline; call routing and no-response behaviour Open (D-07/Q-34–Q-35). |
| Restaurant Manager / Staff | View and manage incoming reservations and pre-orders; manage availability/menu only where explicitly proposed and authorized. | Restaurant-scoped; exact role matrix and transitions Open (D-11/Q-33a–c, P-STAFF). |
| Restaurant Owner / Operator | Operate a table-service restaurant as a separate business entity and integrate agreed restaurant-side systems. | Separate business entities (AC-004); chain-wide access and cross-country sharing unspecified; operational and accounting scope Open (D-02/Q-15a–c, Q-18). |
| POS / Restaurant Automation Provider | Exchange booking, order, and table-availability information with Tabiora. | Integration contracts and recovery rules Open (D-02, D-05, D-06, D-10); no POS/vendor selected. |
| Payment Provider | Process payments and return verifiable results/references. | Provider, protocol, settlement currency, retry/refund, and credential design Open (D-04/D-05). |

## A.4 Target organizations and audience

Target organizations are **table-service restaurants** needing customer reservation, pre-order, service-call, and bill-payment workflows. The specific restaurant *type* for the first release remains Open (D-01/Q-02).

Target audience: mobile customers on supported modern mobile devices (NFR-007, AC-003) and restaurant staff using staff-facing management and notification workflows. The supported device/browser matrix is TBD (D-08/Q-45a–b) — no version list is approved.

Tabiora must support multiple restaurants as separate business entities (AC-004), and staff access must be authenticated, authorized, and scoped to the staff member’s authorized restaurant (NFR-006, P-STAFF).

## A.5 Scope

Must Have functional baseline (priorities and IDs unchanged from the FR registry):

| Scope area | Requirements |
| --- | --- |
| Discovery and filtering | Restaurant search and filtering by dishes, price, distance, and supported parameters (FR-001, FR-002; additional filter parameters Open — D-01/Q-14). |
| Information and menu | Restaurant information, menu, and floor plan with table locations and availability (FR-003, FR-004, FR-006; mandatory fields and content ownership Open — D-10/Q-21). |
| Reservations | Select and book a table for a date and time, with conflict handling (FR-005, NFR-004; authority ownership Open — D-02/Q-20). |
| Pre-orders and prepayment | Select dishes, create a pre-order for a specific time, prepay via a supported payment provider (FR-007, FR-008; checkout and payment lifecycle Open — D-04/Q-19a–c, Q-29–Q-32). |
| Restaurant handoff | Send confirmed reservations and pre-orders to the restaurant, with delivery status tracked separately from booking/payment success (FR-009; acknowledgement/recovery Open — D-06/Q-23). |
| In-restaurant service | Call a waiter from the phone; pay the restaurant bill by phone (FR-010, FR-011; presence evidence, routing, bill ownership Open — D-05, D-07/Q-22, Q-34–Q-36). |
| Staff operations | View and manage reservations and pre-orders; receive waiter-call notifications (FR-012–FR-014; action lists and role allocation Open — D-11/Q-33a–c). |

Should Have and Could Have requirements are planned enhancements, not Must Have baseline: occupancy display, adaptive multimedia menu, activity history, preference profiles, recommendations, modification deadlines, average occupancy, dish advice, additional in-restaurant ordering, favourites, ratings (FR-015–FR-026; D-09, D-12). They are in scope only if selected for a release and approved through the decision register.

Excluded or deferred from confirmed detailed scope: customer blacklist management (FR-027, excluded from initial release); loyalty points, generic login APIs, tax/discount algorithms, food-refusal and refund operations (D-13, deferred, not approved). FR-024–FR-026 remain Could Have; questions about them do not add them to release scope.

## A.6 Out of scope

- Customer blacklist management by restaurant staff (FR-027).
- Warehouse/inventory management, payroll, kitchen scheduling, or other restaurant ERP functions not listed in the FR registry.
- Unapproved legacy details: loyalty points, generic login APIs, tax/discount algorithms, food-refusal and refund operations (D-13).
- International rollout or country-specific expansion; China is only an example in a future expansion assessment.
- A finished implementation, final technology stack, deployment topology, or vendor selection.
- Claims that pilot planning figures are forecasts or approved business requirements.
- Automatic fulfilment or automatic rebooking when a late payment success arrives for a cancelled, expired, or ineligible order/visit.

## A.7 Geography, scale, and workload

No approved geography or rollout plan exists (D-01/Q-03). The system is designed for multiple restaurants as separate business entities (AC-004).

**P-SCALE is an illustrative planning envelope only** — synthetic accounts, tables, concurrent sessions, daily mix, and one-minute bursts. It must not be presented as a forecast, a stakeholder commitment, or an approved requirement (D-01, D-08; Q-04–Q-09).

Numerical acceptance targets are **not approved**:

- NFR-001 reservation response, NFR-002 waiter-call delivery, NFR-009 search latency, NFR-010 menu latency: **TBD**, with Proposed evaluation targets listed only in the measurable QA document and always labelled as unapproved.
- NFR-003 availability: the operating window is 24/7 except scheduled maintenance; **this is not a 100 % uptime claim and no 99.9 % figure is approved**. Any availability figure is TBD (D-08/Q-43–Q-44).
- NFR-007 device/browser matrix: **TBD** (D-08/Q-45a–b).

## A.8 Assumptions and constraints

### Approved invariants (from NFR/AC text)

- All booking channels coordinate through one authoritative booking authority; overlapping confirmed reservations for the same table are impossible (NFR-004). *Ownership* of that authority remains Open (D-02/Q-20).
- Sensitive customer and payment data are transmitted over encrypted connections (NFR-005).
- Restaurant management requires authenticated and authorized access (NFR-006); restaurants are separate business entities (AC-004); no role assignment crosses restaurant boundaries.
- Important booking, order, and payment operations are logged (NFR-008). Required event fields, retention, and readers remain TBD.
- Booking and payment functionality operate 24/7 except scheduled maintenance (NFR-003); 24/7 ≠ 100 % uptime.
- Customer-facing functionality is usable on supported modern mobile devices (NFR-007, AC-003); the matrix is TBD.
- The system can integrate with restaurant automation/POS systems where required (AC-001) and exchanges booking, order, and availability information (AC-005). *Which* systems remains Open (D-02/Q-16).
- External integrations should be isolated from core business logic (AC-006, recommended).
- Card processing is delegated to a certified external provider; raw card details are not stored (AC-002, recommended).

### Proposed, not approved

- P-BOOK, P-PAY, P-INTEGRATION, P-CALL, P-STAFF: proposed lifecycle and permission models with dependencies in D-02–D-07, D-10–D-11. No proposal becomes scope by appearing in a diagram.
- P-SCALE: illustrative planning envelope (D-01, D-08).
- Reservation and pre-order modification (FR-020–FR-021) is Should Have; editable fields, deadlines, and paid-order adjustments Open (D-03/Q-24–Q-27).
- Additional dishes, favourites, feedback (FR-024–FR-026) are Could Have; selection and details Open (D-09/Q-37a–b, Q-38).
- Menu editing by restaurant staff is a candidate capability only (D-10/Q-21).
- Proposed evaluation targets for performance/availability/mobile QA are measurement benchmarks, not approvals.

### Open inputs (not to be invented)

The decision register tracks D-01–D-13 and Q-01–Q-48. The elicitation question set was re-audited for this report:

- Compound questions were split: Q-15 → Q-15a/b/c; Q-19 → Q-19a/b/c; Q-33 → Q-33a/b/c; Q-37 → Q-37a/b; Q-45 → Q-45a/b.
- Premature solution imposition was removed from Q-17 (staff management access, not “a separate interface”), Q-20 (authoritative coordination mechanism, not a named owner), and Q-36 (acceptable presence evidence, not a prescribed mechanism).
- No answers have been recorded. Every question remains **Open**. Any mock interview produced for the Process section must be labelled `MOCK — not stakeholder evidence` and must not update this register.

## A.9 Open inputs summary

- **D-01 / Q-01–Q-14:** target type, geography, restaurant/customer counts, launch, budget, release strategy, first-release scope, additional filters.
- **D-02 / Q-15a–c, Q-16, Q-17, Q-18, Q-20:** restaurant operational scope, existing-system contracts, booking authority, time-zone/interval semantics, management access boundaries, accounting scope.
- **D-03 / Q-24–Q-28, Q-30:** modification fields and deadlines, cancellation, abandoned-checkout handling, checkout-cancellation effect on the reservation.
- **D-04 / Q-19a–c, Q-29, Q-31–Q-32, Q-48:** payment services, draft expiry, cancel/pay races, UNKNOWN reconciliation, late-success review, refunds, review ownership.
- **D-05 / Q-22:** quote/currency, price changes, taxes/discounts, bill source, adjustments, reconciliation.
- **D-06 / Q-23:** handoff acknowledgement, recovery, escalation, undelivered confirmed information.
- **D-07 / Q-34–Q-36:** call recipients, no-response handling, acceptable presence evidence.
- **D-08 / Q-06–Q-09, Q-39–Q-45b:** latency/availability targets, maintenance conditions, mobile matrix, workload profiles, test evidence.
- **D-09 / Q-37a–b, Q-38, Q-47:** occupancy, activity/profile, retention/access, recommendations, favourites/feedback, additional orders.
- **D-10 / Q-21:** restaurant/menu/floor-plan content ownership and menu editing.
- **D-11 / Q-33a–c:** restaurant-specific employee role sets and identity mechanism.
- **D-12 / Q-46:** adaptive multimedia capability/quality and fallback.
- **D-13:** unapproved legacy details (loyalty, generic login APIs, tax/discount algorithms, food refusal, refunds) — deferred.
