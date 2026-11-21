# Non-Functional Requirements

Recorded MoSCoW priorities apply to the requirements below.

## Must Have

- **NFR-001** — A table reservation request shall be processed within the agreed maximum response time.

- **NFR-002** — A waiter-call notification shall be delivered to restaurant staff within the agreed maximum time.

- **NFR-003** — Booking and payment functionality shall be available 24/7 except during scheduled maintenance.

- **NFR-004** — The system shall prevent double booking of the same table for overlapping time intervals.

- **NFR-005** — Sensitive customer and payment-related information shall be transmitted using encrypted connections.

- **NFR-006** — Restaurant management functionality shall require authenticated and authorized access.

- **NFR-007** — Customer-facing functionality shall be usable on supported modern mobile devices.

- **NFR-008** — Important booking, order and payment operations shall be logged.

## Should Have

- **NFR-009** — Restaurant search results should be returned within the agreed target response time.

- **NFR-010** — Menu content should load within the agreed target response time under normal conditions.

- **NFR-011** — The architecture should support adding new restaurants without redesigning the core system.

## Could Have

- **NFR-012** — Multimedia menu content could dynamically adapt its level of detail to the user's connection quality.

## Won't Have

No confirmed non-functional requirements are excluded from the current release.

## Undefined values

| Requirement | Value to be agreed |
| --- | --- |
| NFR-001 | Maximum reservation response time: **TBD** |
| NFR-002 | Maximum waiter-call delivery time: **TBD** |
| NFR-009 | Target restaurant-search response time: **TBD** |
| NFR-010 | Target menu-load response time and normal conditions: **TBD** |

Supported modern mobile devices (NFR-007), scheduled maintenance arrangements (NFR-003), and any numerical availability target remain unspecified (**TBD**). See the [open questions](open-questions.md).

## Verification conditions

Planned verification. Open parameters require agreement before acceptance.

| ID | Measurement or verification method | Open parameters |
| --- | --- | --- |
| NFR-001 | Measure from receipt of a valid reservation request at the Tabiora boundary to the definitive success/conflict/error response. Report each outcome separately, including authoritative integration time. | Maximum time, percentile, workload, dataset, network profile, and treatment of invalid requests: TBD |
| NFR-002 | Correlate call creation with receipt at the agreed staff endpoint. Record dispatch time, endpoint receipt, and human acknowledgement separately; include routing/retry time in end-to-end delivery measurements. | Delivery endpoint, maximum time, recipient policy, and offline behavior: TBD |
| NFR-003 | Define monitored booking/payment probes and report successful service time over the agreed observation window, separately identifying scheduled maintenance and external outages. Never equate an unknown payment with success. | Numerical availability target, maintenance notice/windows, probe cadence, and provider-outage treatment: TBD |
| NFR-004 | Submit simultaneous overlapping requests for one table; assert at most one CONFIRMED reservation. Repeat through every channel using the same authoritative booking source. Test rescheduling against existing bookings and verify no partial mutation on failure under P-BOOK. | Adjacent-interval/buffer policy and load profile: TBD; cross-channel guarantee depends on D-02 |
| NFR-005 | Inspect customer, staff, provider, and restaurant data paths for encrypted transport; reject insecure access carrying sensitive data. Verify logs do not expose card details or credentials in the proposed design. | Protocol configuration, certificate lifecycle, sensitive-field inventory, and security review criteria: TBD |
| NFR-006 | Exercise unauthenticated, unauthorized, and cross-restaurant requests against every management operation; deny access without changing data. Test approved roles once assigned. | Identity/session mechanism, role matrix approval, audit policy: TBD |
| NFR-007 | Test core discovery, booking, checkout, call, and bill-payment tasks on the agreed mobile browser/device matrix, including readable failure messages. | Devices, browsers, viewport/connectivity profiles, usability/accessibility criteria: TBD |
| NFR-008 | Follow one correlation reference through booking, pre-order, payment results, and delivery failure. Confirm important transitions are attributable and duplicates can be investigated without recording secrets. | Required event fields, retention, authorized readers, and tamper protection: TBD |
| NFR-009 | Measure search request receipt to returned results/empty result/error; report outcome distributions and external lookup time. | Target, percentile, filter mix, dataset, traffic/concurrency, and network profile: TBD |
| NFR-010 | Measure menu request receipt to usable menu content; report initial content and multimedia completion separately. | Target, percentile, menu/media sizes, definition of normal conditions, and network profile: TBD |
| NFR-011 | Walk through onboarding a second restaurant with distinct staff/menu/table data, including an attempted cross-restaurant operation. Identify configuration changes and core-code changes needed. | Supported onboarding/integration variation and acceptance boundary: TBD |
| NFR-012 | Throttle a test connection, verify chosen media detail and fallback, and compare it to FR-016 behavior. | Adaptation thresholds and performance/quality criteria: TBD; priority overlap D-12 remains open |

## Proposed reliability checks

These failure-injection checks cover P-BOOK, P-PAY, P-CALL, and P-INTEGRATION.

- Inject authoritative booking-source failure: return `SOURCE_UNAVAILABLE` when no authoritative result can be obtained. Require authoritative evidence before reporting booking success.
- Inject a payment timeout and duplicate/out-of-order results: retain UNKNOWN until verified reconciliation, apply success once, and never regress SUCCEEDED.
- Inject restaurant delivery failure after booking/payment success: preserve domain status and expose a separate failed delivery for staff follow-up.
- Inject staff endpoint failure: retain the waiter call with DELIVERY_FAILED; a customer-facing acceptance message must not claim a waiter has received it.
- Verify retry/reconciliation operations are bounded by a policy once one is agreed; timeout, retry, retention, and recovery-time values remain TBD.

Record environment, dataset, workload, concurrency, network, observation window, failures, and design revision. Use results to characterize behavior until D-08 defines acceptance targets.

## Availability: operating hours versus measured service

NFR-003 defines a 24/7 operating window with scheduled maintenance. Report calendar availability and, separately, availability excluding approved maintenance. Record the denominator, exclusions, probe interval, and partial or provider outages. Unexpected outages remain failures. A valid booking conflict counts as a correct response. An unreachable authority is a service failure, and payment `UNKNOWN` remains a pending financial outcome. Numerical targets and maintenance allowances remain open.

## Mobile compatibility criteria

Agree the device, OS, browser, viewport, accessibility, and network matrix under D-08. Record exact versions and run touch-based discovery, table selection, menu, booking, checkout including cancellation and abandonment, waiter call, and bill payment. Check for clipped controls and readable error messages.

## Quality attributes in Tabiora

| Attribute | Tabiora-specific meaning | Verification evidence |
| --- | --- | --- |
| Performance | Response and notification times at the boundaries defined above, under an explicit workload | Outcome-aware latency distributions; thresholds and percentile remain TBD |
| Reliability | No conflicting confirmed booking, truthful payment outcomes, recoverable handoff, and bounded abandoned checkout lifecycle | Concurrency, timeout, late-result, cancellation-race, and failure-injection scenarios under P-BOOK/P-PAY |
| Security | Authorized restaurant-scoped operations and sensitive data protected in transit | Role-set and cross-restaurant denial tests; encrypted-path checks |
| Observability | Trace a reservation, order, attempt, call, and restaurant handoff through relevant state changes | NFR-008 correlation records, failed-delivery/UNKNOWN/REVIEW_REQUIRED visibility; retention/alert thresholds TBD |
| Testability | Reproduce source outages, conflicts, duplicate/late events, expiry, and different staff assignments without real charges | Controlled prototype/provider/POS simulations; production integration evidence remains separate |
| Scalability | Add separate restaurants and evaluate increasing customers, sessions, request rates, and integration variation | Load/isolated-access tests based on a declared workload, not a generic medium-scale label |

## Illustrative workload and lifecycle tests

Use [P-SCALE](../overview.md#target-businesses-and-planning-scale) for synthetic accounts and tables, concurrent sessions, daily mix, and one-minute bursts. Record traffic distribution and test same-table contention independently. Search, menu, call, and payment rates and acceptance thresholds remain open in D-08.

Additionally test: cancel before any payment attempt; leave a draft until its finite proposed expiry; close the app after initiation; expire/cancel the visit before a late verified success; and recover unfinished effects without rebooking or duplicate fulfilment. An expired/cancelled order receiving payment success must enter REVIEW_REQUIRED rather than disappearing or being fulfilled automatically.

A role test must distinguish WAITER-only, MANAGER-only, both roles in one restaurant, and a different role set in another restaurant. No role assignment crosses restaurant boundaries.
