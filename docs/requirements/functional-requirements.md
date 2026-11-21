# Functional Requirements

Requirements retain their original MoSCoW priorities. Identifiers are stable references.

## Must Have

- **FR-001** — The system shall allow customers to search for restaurants.

- **FR-002** — The system shall allow customers to filter restaurants by dishes, price, distance and other supported parameters.

- **FR-003** — The system shall allow customers to view restaurant information.

- **FR-004** — The system shall display a restaurant floor plan with table locations and availability.

- **FR-005** — The system shall allow customers to select and book a table for a specific date and time.

- **FR-006** — The system shall allow customers to view the restaurant menu.

- **FR-007** — The system shall allow customers to select dishes and create a pre-order for a specific time.

- **FR-008** — The system shall allow customers to prepay an order using a bank card or supported payment system.

- **FR-009** — The system shall send confirmed reservations and pre-orders to the restaurant.

- **FR-010** — The system shall allow customers inside the restaurant to call a waiter using their phone.

- **FR-011** — The system shall allow customers to pay the restaurant bill using their phone.

- **FR-012** — Restaurant staff shall be able to view and manage incoming reservations.

- **FR-013** — Restaurant staff shall be able to view and manage customer pre-orders.

- **FR-014** — Restaurant staff shall receive waiter-call notifications.

## Should Have

- **FR-015** — The system should display the current restaurant occupancy.

- **FR-016** — The system should provide multimedia menu content with a level of detail adapted to the customer's connection speed.

- **FR-017** — The system should record relevant customer actions and order history.

- **FR-018** — The system should create a customer preference profile based on recorded activity.

- **FR-019** — The system should recommend restaurants and dishes based on customer preferences.

- **FR-020** — The system should allow customers to modify an existing table reservation before a defined deadline.

- **FR-021** — The system should allow customers to modify a pre-order before a defined deadline.

## Could Have

- **FR-022** — The system could display average restaurant occupancy for the selected day of the week.

- **FR-023** — The system could provide customers with advice when choosing dishes.

- **FR-024** — The system could allow customers to order additional dishes through the application while already sitting in the restaurant.

- **FR-025** — The system could allow customers to add restaurants or dishes to favorites.

- **FR-026** — The system could allow customers to like, dislike or rate restaurants and dishes.

## Won't Have

- **FR-027** — Customer blacklist management by restaurant staff. **Release status: Excluded from the initial release.**

## Undefined details

The modification deadlines in FR-020 and FR-021 are **TBD**. The additional supported filtering parameters in FR-002 are not enumerated. See the [open questions](open-questions.md).

## Interpretation and verification

These checks define observable outcomes for the recorded baseline. Unagreed parameters remain TBD.

| Requirement | Observable acceptance check |
| --- | --- |
| FR-001 | A search returns matching restaurant records, an empty result, or an explicit failure; an empty list is distinguishable from a failed request. |
| FR-002 | For agreed dish, price, and distance filters, every displayed match satisfies the selected criteria. Units, distance origin, combination rules, and additional parameters are TBD (D-01). |
| FR-003 | Selecting a restaurant displays information for that restaurant. Mandatory fields and content ownership are TBD (D-10). |
| FR-004 | The floor plan identifies tables in the chosen restaurant and shows availability for the selected interval; stale or unavailable data must not be represented as a confirmed booking. |
| FR-005 | A successful request identifies the customer, restaurant, table, and interval. Concurrent overlapping requests cannot both create bookings (NFR-004). Invalid inputs and conflicts produce distinct outcomes. Proposed validation rules are in P-BOOK. |
| FR-006 | The selected restaurant's menu is shown; unavailable menu data has an explicit failure outcome. Price/content freshness and unavailable-dish presentation are TBD. |
| FR-007 | Selected dishes and quantities are associated with the intended visit and requested ready time. The displayed order matches the selections. Whether all pre-orders require a reservation is proposal P-PAY. |
| FR-008 | The customer can initiate supported prepayment and see its known outcome. Initiating a request is not evidence of success. Provider methods, settlement currency, and retry rules are TBD (D-04–D-05). |
| FR-009 | Confirmed reservation and pre-order information reaches the intended restaurant. A failed handoff is recorded separately from booking/payment success; transport, acknowledgement, and recovery are D-06. |
| FR-010 | A call identifies the intended restaurant/table context and reports acceptance for routing separately from staff notification. How the customer's presence is verified is TBD (D-07). |
| FR-011 | A customer can review the restaurant bill amount and initiate payment by phone. Bill ownership, adjustments, split payments, and POS reconciliation remain D-05; pre-order payment alone does not satisfy this requirement. |
| FR-012 | Authorized staff can view and perform permitted management actions on incoming reservations for their restaurant. Action list and role allocation are D-11/P-STAFF. |
| FR-013 | Authorized staff can view and perform permitted management actions on their restaurant's pre-orders. Order changes, preparation states, and assignment are not yet agreed. |
| FR-014 | The intended staff recipient receives a notification for a customer call. System dispatch, device receipt, and human acknowledgement are distinct; endpoint and deadline are D-07–D-08. |
| FR-015 | Current occupancy is displayed for the selected restaurant with a defined observation time. Formula and authoritative data are TBD. |
| FR-016 | Under agreed connection profiles the multimedia menu selects the corresponding detail level. Profiles and fallback content are D-12. |
| FR-017 | Agreed customer actions and order history can be recorded and associated with the correct customer; event list, access, consent, and retention are D-09. |
| FR-018 | A profile reflects agreed recorded preferences/activity for the correct customer; empty-history behavior and controls are D-09. |
| FR-019 | Recommendations identify restaurants/dishes and use the intended customer's preferences. Evaluation criteria and fallback without a profile are TBD; recommendation quality is not established by drawing a class. |
| FR-020 | Before an agreed deadline a permitted reservation change can be requested. A conflicting change must not create double booking. Deadline, editable fields, and original-booking retention are D-03/P-BOOK. |
| FR-021 | Before an agreed deadline a permitted pre-order change can be requested. Handling an already-paid amount difference is TBD (D-04). |
| FR-022 | Average occupancy corresponds to the selected weekday and an agreed sampling period/formula; these remain TBD. |
| FR-023 | Advice is presented during dish selection if this capability is selected. Advice source and validation criteria remain TBD. |
| FR-024 | If selected, an in-restaurant order records additional dishes for the correct visit. This does not make additional ordering part of the Must Have baseline. |
| FR-025 | If selected, favorite restaurants/dishes are associated with the intended customer and remain retrievable. Persistence and removal rules are TBD. |
| FR-026 | If selected, supported feedback is associated with the correct restaurant/dish. Rating scale, eligibility, and moderation are TBD. |
| FR-027 | The initial-release staff interface and permissions provide no customer blacklist management. |

## Proposed lifecycle model

These lifecycle rules are **proposed**. See the [decision register](open-questions.md) for adoption and timing dependencies.

| Proposal | Model | Proposed transitions and safeguards |
| --- | --- | --- |
| P-BOOK | Reservation: CONFIRMED, CANCELLED, EXPIRED | Atomic booking creates CONFIRMED independently of prepayment. Cancellation is a separate authorized visit operation, not an effect of leaving checkout. At the booked interval's end, CONFIRMED becomes EXPIRED. Only a CONFIRMED reservation within its finite interval blocks that table. |
| P-PAY | PreOrder: DRAFT, AWAITING_PAYMENT, PAID, CANCELLED, EXPIRED, REVIEW_REQUIRED | DRAFT contains selected items; a valid quote is required to initiate payment. Initiation produces AWAITING_PAYMENT. Verified success produces PAID only while the order and reservation remain eligible; otherwise REVIEW_REQUIRED. Unpaid cancellation/expiry is allowed only when no attempt is unresolved and none succeeded. |
| P-PAY | Payment: INITIATED, SUCCEEDED, FAILED, UNKNOWN | A payment record exists only after initiation. Verified results establish SUCCEEDED or FAILED. Missing/untrusted/non-final results become UNKNOWN; reconciliation resolves them. A browser exit is not a verified payment rejection or provider cancellation. |
| P-INTEGRATION | DeliveryStatus: PENDING, DELIVERED, FAILED | Restaurant acknowledgement establishes DELIVERED. Handoff failure is separate from reservation/payment status; recover using the same reference. |
| P-CALL | WaiterCall: CREATED, NOTIFIED, ACKNOWLEDGED, RESOLVED, DELIVERY_FAILED | Endpoint receipt establishes NOTIFIED. A staff member with the WAITER role acknowledges and resolves the call within the authorized restaurant. Re-routing preserves the call reference; timing/escalation remains TBD. |

P-BOOK proposes a positive guest count, capacity checks, and a valid half-open interval [start, end). Failed changes retain the original booking. The end instant releases the table even if the state update is delayed. D-02/D-03 cover local-time rules, cleaning buffers, cancellation, no-shows, and early release. A future hold policy would need a finite deadline and explicit release operation.

P-PAY proposes one pre-order per reservation, positive quantities, item-price snapshots, and a quote bound to amount and currency. Abandoned drafts expire at the proposed finite checkout deadline (duration and trigger open in D-04). Drafts add no table hold.

### Checkout exits and financial consistency

| Situation | Reservation | PreOrder | Payment |
| --- | --- | --- | --- |
| Customer explicitly cancels before initiating payment | CONFIRMED until separate visit cancellation or booked end | CANCELLED | No attempt created |
| Customer leaves before initiating payment | CONFIRMED until separate cancellation or booked end | DRAFT until the proposed finite checkout deadline, then EXPIRED | No attempt created |
| Verified payment success while the visit/order remains eligible | CONFIRMED | PAID; handoff tracked separately | SUCCEEDED |
| Verified payment rejection | CONFIRMED | AWAITING_PAYMENT until permitted retry/cancellation/expiry | FAILED |
| Payment times out | CONFIRMED until booked end/cancellation | AWAITING_PAYMENT while eligible | UNKNOWN; reconcile, no new attempt |
| Customer closes the app after initiation | Reservation lifecycle continues independently | AWAITING_PAYMENT while eligible; browser closure does not cancel a provider operation | INITIATED or UNKNOWN; reconcile |
| Checkout deadline passes or visit is cancelled/expires while payment is unresolved | CONFIRMED until its own end, or CANCELLED/EXPIRED through its separate lifecycle; payment does not extend it | REVIEW_REQUIRED; no fulfilment handoff | Preserve INITIATED/UNKNOWN and reconcile |
| Verified success arrives for a cancelled/expired/ineligible order or visit | No automatic rebooking | REVIEW_REQUIRED; no automatic fulfilment | SUCCEEDED; retain financial truth and route for authorized review |

A late verified rejection records `FAILED` and leaves an ineligible order in `REVIEW_REQUIRED` until closure rules are agreed. Refund eligibility and execution, paid-order changes, and review ownership remain TBD.

Initiation checks eligibility and registers the attempt together. Cancellation and expiry check the same state. If initiation wins, reconcile or review the existing attempt. An `INITIATED` or `UNKNOWN` attempt blocks a fresh one. Duplicate results resume unfinished effects without repeating charges, orders, or handoffs. `SUCCEEDED` never regresses. Coordination and scheduling mechanisms remain open.

## Proposed staff permissions

P-STAFF is a least-privilege proposal supporting FR-012–FR-014 and NFR-006. All staff operations are scoped to the staff member's authorized restaurant; customer operations are scoped to the customer's own visit in the prototype. Authentication/authorization for restaurant management is mandatory (NFR-006); the identity mechanism and detailed role assignment remain TBD.

| Action | Customer | WAITER role | MANAGER role |
| --- | --- | --- | --- |
| Create a booking/pre-order; initiate own payment or waiter call | Own visit | No | No |
| View relevant incoming pre-orders | Own order | Assigned restaurant/service context | Authorized restaurant |
| View/manage reservations and pre-orders | Own records; modifications remain Should Have | Pre-order read only as above; no management | Authorized restaurant; exact management transitions TBD |
| Manage table availability | No | No | Proposed; authority rules D-02 |
| Receive, acknowledge, resolve calls | Initiate own call | Authorized restaurant/service context | Monitoring/escalation permission TBD |
| Edit menu information | No | No | Candidate capability only; approval/content ownership D-10 |
| Blacklist customers or change provider payment results | No | No | No |

Waiter acknowledgement/resolution and manager availability/menu editing are proposed refinements, not extra Must Have requirements. Declining a prepared dish, cancelling a paid order, and issuing refunds require explicit policies before any actor is granted those operations.

### Multiple responsibilities per employee

P-STAFF assigns WAITER and MANAGER independently per restaurant membership. A dual-role member can use both roles' permissions in that restaurant. Each request checks the active membership and role. A role at another restaurant confers no access. Role assignment remains proposed under D-11. No default assignment, role hierarchy, or general permission engine is specified.
