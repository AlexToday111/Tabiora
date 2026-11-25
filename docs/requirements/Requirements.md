# FR / NFR / AC Prioritised Register

Status legend:

- **Agreed baseline** — requirement and observable acceptance check exist; no blocking open decision for the baseline behaviour.
- **Partial** — baseline behaviour is agreed, but specific parameters or policies remain TBD (see referenced D-/Q-IDs).
- **Proposed** — behaviour rests on a P-* proposal not yet approved.
- **Excluded** — explicitly out of the initial release.

## Table 1 — Functional Requirements (FR)

### Must Have

| ID | Requirement | MoSCoW | Rationale | Acceptance evidence / status |
| --- | --- | --- | --- | --- |
| FR-001 | Customers can search for restaurants. | Must | Entry point to discovery; core customer journey. | Search returns matching records, an empty result, or explicit failure; empty vs failed is distinguishable. **Agreed baseline.** |
| FR-002 | Customers can filter restaurants by dishes, price, distance, and other supported parameters. | Must | Users choose restaurants by practical constraints and intended dishes. | Every displayed match satisfies the agreed filters (dish, price, distance). Additional parameters, units, distance origin, and combination rules TBD (D-01/Q-14). **Partial.** |
| FR-003 | Customers can view restaurant information. | Must | Needed to decide whether to book. | Selecting a restaurant displays information for that restaurant. Mandatory fields and content ownership TBD (D-10/Q-21). **Partial.** |
| FR-004 | System displays a restaurant floor plan with table locations and availability. | Must | Supports table selection; reduces uncertainty about availability. | Floor plan identifies tables in the chosen restaurant and shows availability for the selected interval; stale/unavailable data must not appear as confirmed booking. Freshness and authority TBD (D-02, D-10). **Partial.** |
| FR-005 | Customers can select and book a table for a specific date and time. | Must | Core visit workflow. | Successful booking identifies customer, restaurant, table, and interval; overlapping requests cannot both create bookings (NFR-004); invalid and conflict outcomes are distinct. Validation rules rest on proposed P-BOOK; authority ownership TBD (D-02/Q-20). **Proposed / Partial.** |
| FR-006 | Customers can view the restaurant menu. | Must | Needed for pre-order and discovery. | Selected restaurant’s menu shown; unavailable menu data has explicit failure outcome. Price/content freshness and unavailable-dish presentation TBD (D-05, D-10). **Partial.** |
| FR-007 | Customers can select dishes and create a pre-order for a specific time. | Must | Reduces ordering delays and supports kitchen preparation. | Selected dishes and quantities associated with intended visit and requested ready time; displayed order matches selections. Whether all pre-orders require a reservation is proposed in P-PAY; eligibility rules TBD (D-03–D-04). **Proposed / Partial.** |
| FR-008 | Customers can prepay an order using a bank card or supported payment system. | Must | Reduces payment friction before the visit. | Customer can initiate supported prepayment and see its known outcome; initiation is not evidence of success. Provider methods, settlement currency, and retry rules TBD (D-04–D-05/Q-19). **Partial.** |
| FR-009 | System sends confirmed reservations and pre-orders to the restaurant. | Must | Restaurant must receive confirmed information to prepare. | Confirmed reservation/pre-order information reaches intended restaurant; failed handoff recorded separately from booking/payment success. Transport, acknowledgement, and recovery TBD (D-06/Q-23, P-INTEGRATION). **Proposed / Partial.** |
| FR-010 | Customers inside the restaurant can call a waiter using their phone. | Must | Removes difficulty of contacting staff. | Call identifies intended restaurant/table context; acceptance for routing reported separately from staff notification. Presence verification TBD (D-07/Q-36). **Partial.** |
| FR-011 | Customers can pay the restaurant bill using their phone. | Must | Reduces checkout delay. | Customer can review restaurant bill amount and initiate payment by phone. Bill ownership, adjustments, split payments, and POS reconciliation TBD (D-05/Q-22); pre-order payment alone does not satisfy this requirement. **Partial.** |
| FR-012 | Restaurant staff can view and manage incoming reservations. | Must | Restaurants must coordinate incoming guests. | Authorized staff can view and perform permitted management actions on incoming reservations for their restaurant. Action list and role allocation TBD (D-11/Q-33, P-STAFF). **Proposed / Partial.** |
| FR-013 | Restaurant staff can view and manage customer pre-orders. | Must | Supports preparation and coordination. | Authorized staff can view and perform permitted management actions on their restaurant’s pre-orders. Order changes, preparation states, and assignment not yet agreed. **Proposed / Partial.** |
| FR-014 | Restaurant staff receive waiter-call notifications. | Must | Without notification, FR-010 provides no service outcome. | Intended staff recipient receives a notification for a customer call; dispatch, endpoint receipt, and human acknowledgement are distinct. Endpoint and deadline TBD (D-07–D-08/Q-34–Q-35, Q-42). **Proposed / Partial.** |

### Should Have

| ID | Requirement | MoSCoW | Rationale | Acceptance evidence / status |
| --- | --- | --- | --- | --- |
| FR-015 | System displays current restaurant occupancy. | Should | Helps customers judge whether to visit; supports load awareness. | Occupancy displayed for selected restaurant with defined observation time. Formula and authoritative data TBD (D-09). **Partial.** |
| FR-016 | Multimedia menu content with detail level adapted to connection speed. | Should | Improves menu quality without excluding slow connections. | Under agreed connection profiles, menu selects corresponding detail level. Profiles and fallback TBD (D-12/Q-46); overlaps with NFR-012. **Partial.** |
| FR-017 | System records relevant customer actions and order history. | Should | Basis for profiles, recommendations, and support. | Agreed customer actions and order history recorded and associated with correct customer. Event list, access, consent, and retention TBD (D-09/Q-47). **Partial.** |
| FR-018 | System creates a customer preference profile based on recorded activity. | Should | Enables personalization. | Profile reflects agreed recorded preferences/activity for correct customer; empty-history behaviour and controls TBD (D-09). **Partial.** |
| FR-019 | System recommends restaurants and dishes based on customer preferences. | Should | Adds discovery value. | Recommendations identify restaurants/dishes and use intended customer’s preferences. Evaluation criteria and fallback without profile TBD; quality not established by a class diagram (D-09). **Partial.** |
| FR-020 | Customers can modify an existing table reservation before a defined deadline. | Should | Reduces no-shows and manual calls. | Before an agreed deadline a permitted reservation change can be requested; conflicting change must not create double booking. Deadline, editable fields, retention TBD (D-03/Q-24–Q-25). **Proposed / Partial.** |
| FR-021 | Customers can modify a pre-order before a defined deadline. | Should | Accommodates last-minute changes. | Before an agreed deadline a permitted pre-order change can be requested. Handling paid amount difference TBD (D-04/Q-26–Q-27). **Proposed / Partial.** |

### Could Have

| ID | Requirement | MoSCoW | Rationale | Acceptance evidence / status |
| --- | --- | --- | --- | --- |
| FR-022 | System could display average restaurant occupancy for the selected day of the week. | Could | Helps customer planning beyond current occupancy. | Average occupancy corresponds to selected weekday and an agreed sampling period/formula; these remain TBD (D-09). **Partial.** |
| FR-023 | System could provide customers with advice when choosing dishes. | Could | Improves selection experience. | Advice presented during dish selection if capability selected. Advice source and validation criteria TBD (D-09). **Partial.** |
| FR-024 | Customers could order additional dishes through the application while seated in the restaurant. | Could | Extends in-visit experience. | In-restaurant order records additional dishes for the correct visit; does not extend Must Have baseline. Details TBD (D-09/Q-38). **Partial.** |
| FR-025 | Customers could add restaurants or dishes to favorites. | Could | Supports repeat visits. | Favorites associated with intended customer and remain retrievable. Persistence and removal rules TBD (D-09/Q-37). **Partial.** |
| FR-026 | Customers could like, dislike, or rate restaurants and dishes. | Could | Feedback channel. | Supported feedback associated with correct restaurant/dish. Rating scale, eligibility, moderation TBD (D-09). **Partial.** |

### Won't Have

| ID | Requirement | MoSCoW | Rationale | Acceptance evidence / status |
| --- | --- | --- | --- | --- |
| FR-027 | Customer blacklist management by restaurant staff. | Won't (initial release) | Excluded from initial release; no initial-release blacklist design is required. | Initial-release staff interface and permissions provide no customer blacklist management. **Excluded.** |

## Table 2 — Non-Functional Requirements (NFR)

### Must Have

| ID | Requirement | MoSCoW | Rationale | Acceptance evidence / status |
| --- | --- | --- | --- | --- |
| NFR-001 | Table reservation request processed within agreed maximum response time. | Must | A slow or ambiguous booking result directly harms the visit workflow. | Measure receipt of valid reservation request at Tabiora boundary to definitive success/conflict/error, reported per outcome, including authoritative integration time. Maximum time, percentile, workload, dataset, network profile, and invalid-request treatment TBD (D-08/Q-39). **Partial.** |
| NFR-002 | Waiter-call notification delivered to restaurant staff within agreed maximum time. | Must | Waiter call is only useful if staff receive it promptly. | Correlate call creation with receipt at agreed staff endpoint; dispatch, endpoint receipt, and human acknowledgement recorded separately. Endpoint, max time, recipient policy, offline behaviour TBD (D-07–D-08/Q-34, Q-42). **Partial.** |
| NFR-003 | Booking and payment functionality available 24/7 except scheduled maintenance. | Must | Customers plan outside business hours; payment cannot be silently unavailable. | Monitored booking/payment probes; report successful service time over agreed window, identifying scheduled maintenance and external outages separately; UNKNOWN payment never counted as success. Numerical availability target, maintenance notice/windows, probe cadence, provider-outage treatment TBD (D-08/Q-43–Q-44). **Partial.** |
| NFR-004 | Prevent double booking of the same table for overlapping intervals. | Must | Booking integrity; basis of customer trust. | Simultaneous overlapping requests for one table produce at most one CONFIRMED reservation, tested through every channel against the same authoritative source; reschedule against existing bookings verified with no partial mutation under proposed P-BOOK. Authority ownership TBD (D-02/Q-20); adjacent-interval/buffer policy TBD. **Partial.** |
| NFR-005 | Sensitive customer and payment-related information transmitted over encrypted connections. | Must | Protect personal and payment data in transit. | Inspect customer, staff, provider, and restaurant data paths for encrypted transport; reject insecure access carrying sensitive data; logs must not expose card details or credentials. Protocol configuration, certificate lifecycle, sensitive-field inventory, security review criteria TBD. **Partial.** |
| NFR-006 | Restaurant management functionality requires authenticated and authorized access. | Must | Prevents unauthorized management and cross-restaurant access. | Unauthenticated, unauthorized, and cross-restaurant requests against every management operation are denied without data change; approved roles tested once assigned. Identity/session mechanism and role matrix TBD (D-11/Q-33). **Partial.** |
| NFR-007 | Customer-facing functionality usable on supported modern mobile devices. | Must | Primary customer channel is a smartphone (AC-003). | Core discovery, booking, checkout, call, and bill-payment tasks tested on agreed mobile browser/device matrix, including readable failure messages. Device/browser/viewport/connectivity/usability/accessibility matrix TBD (D-08/Q-45). **Partial.** |
| NFR-008 | Important booking, order, and payment operations logged. | Must | Auditability and investigation of failed/duplicate/late outcomes. | One correlation reference traceable through booking, pre-order, payment results, and delivery failure; important transitions attributable; duplicates investigable without secrets. Required event fields, retention, authorized readers, tamper protection TBD. **Partial.** |

### Should Have

| ID | Requirement | MoSCoW | Rationale | Acceptance evidence / status |
| --- | --- | --- | --- | --- |
| NFR-009 | Restaurant search results returned within agreed target response time. | Should | Search is the discovery entry point; slow search degrades experience. | Measure request receipt to returned results/empty/error; report distributions and external lookup time. Target, percentile, filter mix, dataset, traffic/concurrency, and network profile TBD (D-08/Q-40). **Partial.** |
| NFR-010 | Menu content loads within agreed target response time under normal conditions. | Should | Menu is prerequisite for pre-order. | Measure request receipt to usable menu content; report initial content and multimedia completion separately. Target, percentile, menu/media sizes, normal conditions, and network profile TBD (D-08/Q-41). **Partial.** |
| NFR-011 | Architecture supports adding new restaurants without redesigning the core system. | Should | AC-004 requires multiple restaurants as separate business entities. | Onboard a second restaurant with distinct staff/menu/table data, including an attempted cross-restaurant operation; identify configuration vs core-code changes. Supported onboarding variation and acceptance boundary TBD. **Partial.** |

### Could Have

| ID | Requirement | MoSCoW | Rationale | Acceptance evidence / status |
| --- | --- | --- | --- | --- |
| NFR-012 | Multimedia menu content could dynamically adapt detail level to user’s connection quality. | Could | Graceful degradation for slow connections. | Throttle test connection, verify chosen media detail and fallback, compare with FR-016 behaviour. Adaptation thresholds and performance/quality criteria TBD (D-12/Q-46); priority overlap with FR-016 remains open. **Partial.** |

### Won't Have

No NFRs are excluded from the current release. (Confirmed in the recorded NFR file.)

## Table 3 — Architectural Constraints (AC)

AC wording uses “must” for a constraint and “should” for a recommendation. MoSCoW is mapped as Must = mandatory constraint, Should = recommended constraint; it does not override the FR/NFR priorities.

| ID | Constraint | Strength / MoSCoW-equivalent | Rationale | Acceptance evidence / status |
| --- | --- | --- | --- | --- |
| AC-001 | System must be able to integrate with existing restaurant automation/POS systems where required. | Must | Restaurants already operate POS systems; without integration the platform duplicates work. | Integration capability demonstrated for at least one agreed restaurant-side system. Which systems must be connected TBD (D-02/Q-16). **Partial.** |
| AC-002 | Payment card processing should be delegated to a certified external payment provider; raw card details should not be stored in the application. | Should | Reduces PCI scope and security risk. | No raw card details stored or logged in Tabiora; processing handled by provider. Provider, protocol, and credential design TBD (D-04–D-05/Q-19). **Partial.** |
| AC-003 | Customer-facing functionality must be accessible from smartphones. | Must | Target audience is mobile customers. | Same as NFR-007 verification: core tasks usable on agreed mobile matrix. Device/browser matrix TBD (D-08/Q-45). **Partial.** |
| AC-004 | Architecture must support multiple restaurants as separate business entities. | Must | Tabiora serves restaurants as independent businesses. | Second-restaurant onboarding plus cross-restaurant denial test; P-STAFF role scoping per restaurant. Role assignment TBD (D-11/Q-33). **Proposed / Partial.** |
| AC-005 | System must exchange booking, order, and table-availability information with restaurant-side systems. | Must | Restaurant coordination depends on restaurant-side data exchange. | Agreed data contracts exercised for booking, order, and availability exchange; handoff acknowledgement and recovery TBD (D-02, D-06/Q-23, P-INTEGRATION). **Proposed / Partial.** |
| AC-006 | External integrations should be isolated from the core business logic so different restaurant systems can be supported. | Should | Enables different restaurant systems without rewriting core. | Logical ports (BookingAuthority, POSIntegration, PaymentProviderPort, StaffNotifier) are proposed; no POS/vendor selected. **Proposed.** |

## Notes on P-* proposals

P-BOOK, P-PAY, P-INTEGRATION, P-CALL, P-STAFF, and P-SCALE remain **proposals** in the decision register and do **not** create new Must Have scope. Where a row above is marked **Proposed**, the accepted baseline is the FR/NFR text; the P-* content is only the current shape of the proposed solution and must be confirmed through the D-IDs referenced. P-SCALE is an illustrative planning envelope, not an approved workload. No proposal is approved simply because it appears in this register.
