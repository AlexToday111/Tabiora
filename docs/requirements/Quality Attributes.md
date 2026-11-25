# Measurable Quality Attributes

Definitions:

- **Approved** — invariant or agreed baseline behaviour from the recorded NFR/AC text.
- **Proposed evaluation target** — a numerical target suggested as a starting point for measurement, with stated rationale, explicitly not stakeholder-approved.
- **TBD** — no agreed value; measurement method is defined but acceptance threshold remains open.

Workload reference: **P-SCALE** is an illustrative planning envelope only (synthetic accounts, tables, sessions, daily mix, one-minute bursts). Actual workload is **TBD** under D-08 / Q-06–Q-09.

## 1. Measurement conventions

All latency measurements are **outcome-aware**: fast failures are not counted as successful samples. For each NFR, report per-outcome distributions (success, conflict, empty, error, unavailable) and a separate **end-to-end** figure that includes authoritative external time. Invalid-input handling is reported separately from valid-request latency. Environment, dataset, traffic mix, network profile, observation window, and design revision must be recorded with every measurement.

Unless stated otherwise, latency is reported as **p95 and p99 in seconds**, and success metrics as **fraction of attempts within the agreed window**.

## 2. Per-NFR measurement definitions

### 2.1 NFR-001 — Reservation response

| Field | Definition |
| --- | --- |
| Start of measurement | Receipt at Tabiora boundary of a **valid** reservation request (customer, restaurant, table, requested interval, guest count). Invalid requests excluded and reported separately. |
| End of measurement | Definitive response returned to the customer: success, conflict, source-unavailable, or error. |
| Metric | p95 and p99 latency (s) per outcome; success rate; conflict rate; source-unavailable rate. |
| Workload & test data | P-SCALE synthetic restaurants/tables; concurrent single-table contention and non-contending reservations mixed; authoritative booking-source integrated or simulated. |
| Error / external-delay inclusion | Authoritative booking-authority time included in end-to-end. `SOURCE_UNAVAILABLE` is reported as an error outcome, not as a latency sample. Invalid-request treatment reported separately. |
| Target | **TBD** (D-08 / Q-39). Proposed evaluation target: **p95 ≤ 2 s, p99 ≤ 5 s** end-to-end under normal conditions. Rationale: interactive mobile booking; users typically abandon beyond a few seconds; a 2 s p95 budget forces the authoritative source to respond quickly. This is a proposal, not an approval. |
| Status | TBD / Proposed evaluation target |

### 2.2 NFR-002 — Waiter-call delivery

| Field | Definition |
| --- | --- |
| Start of measurement | Creation of a waiter call (FR-010) with a valid restaurant/table context. |
| End of measurement | Receipt at the agreed staff endpoint (device-level). **Human acknowledgement is a separate metric** and is not counted as delivery. |
| Metric | p95 and p99 delivery time (s), including routing/retry; endpoint-receipt success rate; `DELIVERY_FAILED` rate. |
| Workload & test data | Single call and bursts (e.g. several simultaneous calls within one restaurant minute); WAITER endpoint per restaurant. Offline-endpoint behaviour reported separately. |
| Error / external-delay inclusion | Routing/retry time included in end-to-end. `DELIVERY_FAILED` counted as failure, not as slow sample. Human acknowledgement (if measurable) reported separately. |
| Target | **TBD** (D-08 / Q-42, with D-07 recipient policy). Proposed evaluation target: **p95 ≤ 5 s, p99 ≤ 15 s** end-to-end including routing/retry. Rationale: in-room service interaction; the system-controllable boundary is endpoint receipt, not human response. This is a proposal, not an approval. |
| Status | TBD / Proposed evaluation target |

### 2.3 NFR-003 — Booking and payment availability

| Field | Definition |
| --- | --- |
| Start of measurement | Scheduled probe at the agreed booking and payment boundary (separate probes per boundary). |
| End of measurement | Probe outcome recorded as success, expected business outcome (e.g. valid booking conflict), or failure (including source-unavailable and provider-unavailable). |
| Metric | Successful-probe fraction over the observation window, reported as **calendar availability** and **excluding-approved-maintenance availability**, separately. Also record: unexpected outage minutes, provider-outage minutes, `UNKNOWN` payment outcomes (counted as pending financial outcome, never as success). |
| Workload & test data | Agreed probe cadence per boundary; observation window at least the reporting period. Provider-outage scenarios injected separately. |
| Error / external-delay inclusion | Scheduled maintenance excluded only if announced under the agreed maintenance policy. Unexpected outages and provider outages counted as failures unless separately categorized (and the separate category must be stated). A valid booking conflict counts as a correct response, not a failure. An unreachable authority counts as a service failure. |
| Target | **TBD** (D-08 / Q-43–Q-44). **24/7 operating window ≠ 100 % uptime** and this document does not claim 99.9 %. Proposed evaluation target for a pilot-scale deployment: **99.5 % calendar-monthly availability excluding approved maintenance, reported separately from including-maintenance availability**, with a stated provider-outage treatment. Rationale: a single-region pilot with external booking and payment dependencies cannot honestly claim 99.9 % without proven redundancy and provider SLAs. This is a proposal, not an approval. |
| Status | TBD / Proposed evaluation target |

### 2.4 NFR-004 — No double booking

| Field | Definition |
| --- | --- |
| Start of measurement | N simultaneous valid reservation requests for the **same table and overlapping interval**, submitted through every booking channel that the chosen authority controls. |
| End of measurement | Final authoritative outcome for each request recorded. |
| Metric | Number of `CONFIRMED` reservations across the attempt set (must be ≤ 1); number of `SOURCE_UNAVAILABLE` outcomes (not counted as conflicts). |
| Workload & test data | At least the concurrency level agreed under D-08; test same-table contention independently of other load; include reschedule against an existing booking. |
| Error / external-delay inclusion | Underlying source failure must not produce `CONFIRMED`; a source-unavailable outcome is reported separately. |
| Target | **At most 1 CONFIRMED reservation** across concurrent overlapping requests. Approved as an invariant from NFR-004 wording; the cross-channel guarantee depends on authority coordination (D-02). Adjacent-interval/buffer policy TBD. |
| Status | Approved (invariant) |

### 2.5 NFR-005 — Security of sensitive data in transit

| Field | Definition |
| --- | --- |
| Start of measurement | Inventory of customer, staff, provider, and restaurant data paths identified as carrying sensitive data. |
| End of measurement | Inspection / test result per path. |
| Metric | Count of insecure transports carrying sensitive data (target **0**); count of secrets (card details, credentials) observed in logs (target **0**). |
| Workload & test data | Full data-path inventory; log inspection for proposed design; protocol configuration and certificate lifecycle reviewed. |
| Error / external-delay inclusion | Not latency-relevant; any insecure path or leaked secret is a failure, not a degraded success. |
| Target | **Zero** insecure transports and **zero** secrets in logs. Approved as an invariant from NFR-005 wording and AC-002. Sensitive-field inventory and review criteria TBD. |
| Status | Approved (invariant) |

### 2.6 NFR-006 / AC-004 — Authentication, authorization, multi-restaurant access

| Field | Definition |
| --- | --- |
| Start of measurement | Each request to a management operation (view, manage reservations, manage pre-orders, manage availability, edit menu, receive/acknowledge/resolve calls). |
| End of measurement | Response observed and any data mutation/leak recorded. |
| Metric | Count of unauthenticated requests that succeed (target **0**); count of unauthorized-role requests that succeed (target **0**); count of cross-restaurant requests that succeed or leak data (target **0**); count of reads/writes attributed to the wrong restaurant (target **0**). |
| Workload & test data | Full management-operation matrix × {unauthenticated, unauthorized role, cross-restaurant membership, dual-role membership, WAITER-only, MANAGER-only}; approved role matrix once assigned under D-11. |
| Error / external-delay inclusion | Not latency-relevant. Any successful breach is a failure, independent of performance. |
| Target | **Zero** breaches. Approved as invariant from NFR-006 and AC-004; role matrix and identity mechanism TBD. |
| Status | Approved (invariant) |

### 2.7 NFR-007 / AC-003 — Mobile usability

| Field | Definition |
| --- | --- |
| Start of measurement | Start of a defined core task on the agreed mobile browser/device matrix: discovery, table selection, menu, booking, checkout including cancellation and abandonment, waiter call, bill payment. |
| End of measurement | Task completed, abandoned, or failed with an unreadable/absent error message. |
| Metric | Task completion rate per device profile; count of clipped controls; count of unreadable error messages; latency of task-level completion (reported separately from NFR-001/009/010 boundaries). |
| Workload & test data | Agreed device/OS/browser/viewport/connectivity matrix (D-08 / Q-45); touch-based interaction; exact versions recorded. |
| Error / external-delay inclusion | Network conditions on the agreed matrix must be stated. Failures caused by external sources must be shown as readable error outcomes, not as blank screens. |
| Target | **TBD** for matrix and accessibility criteria. Proposed evaluation target: **100 % of core tasks completable on every matrix device, zero clipped controls, readable failure messages throughout**. Rationale: below 100 % completion means at least one supported device cannot perform a Must Have task. This is a proposal, not an approval. |
| Status | TBD / Proposed evaluation target |

### 2.8 NFR-008 — Logging / observability

| Field | Definition |
| --- | --- |
| Start of measurement | A booking or pre-order is created with a correlation reference. |
| End of measurement | Pre-order, payment result, and delivery outcome all resolved (or window closed) under the same reference. |
| Metric | Fraction of defined important transitions traceable via the correlation reference (target **100 %** of the agreed event set); secrets observed in logs (target **0**). |
| Workload & test data | Agreed event list; single correlation reference followed end-to-end; duplicate/late results injected. |
| Error / external-delay inclusion | `UNKNOWN`, `DELIVERY_FAILED`, and `REVIEW_REQUIRED` states must be visible with the same reference; they are not “errors” for this metric. |
| Target | **100 %** traceability of the agreed event set; **zero** secrets in logs. Approved for the invariant parts; event list, retention, authorized readers, tamper protection TBD. |
| Status | Approved (invariant) / Partial |

### 2.9 NFR-009 — Search latency

| Field | Definition |
| --- | --- |
| Start of measurement | Receipt of a search request at the Tabiora boundary. |
| End of measurement | Results, empty result, or error returned to the customer. |
| Metric | p95 and p99 latency (s) per outcome; outcome distribution; external-lookup time reported separately. |
| Workload & test data | Filter mix agreed under D-01/Q-14; dataset per P-SCALE; traffic/concurrency and network profile stated. |
| Error / external-delay inclusion | External lookup time included in end-to-end; failures reported as an outcome, not as fast samples. |
| Target | **TBD** (D-08 / Q-40). Proposed evaluation target: **p95 ≤ 1.5 s, p99 ≤ 3 s** end-to-end under normal conditions. Rationale: search is the discovery entry point; slower first response pushes users out of the funnel before restaurant pages load. This is a proposal, not an approval. |
| Status | TBD / Proposed evaluation target |

### 2.10 NFR-010 — Menu latency

| Field | Definition |
| --- | --- |
| Start of measurement | Receipt of a menu request at the Tabiora boundary. |
| End of measurement | Usable menu content available for interaction (text, prices, dish list); multimedia completion is a separate metric. |
| Metric | p95 and p99 latency (s) for initial usable menu; p95 and p99 for full multimedia completion; unavailable-menu error rate. |
| Workload & test data | Menu/media sizes agreed under D-12/Q-46; definition of “normal conditions” and network profile stated; dataset per P-SCALE. |
| Error / external-delay inclusion | Unavailable menu data must surface as an explicit error outcome, not as an empty menu. |
| Target | **TBD** (D-08 / Q-41). Proposed evaluation target: **p95 ≤ 2 s** for initial usable menu; **p95 ≤ 5 s** for full multimedia completion under normal conditions. Rationale: menu is a prerequisite for pre-order; initial usable content should not wait on large media. This is a proposal, not an approval. |
| Status | TBD / Proposed evaluation target |

### 2.11 NFR-011 / AC-004 — Multi-restaurant architecture

| Field | Definition |
| --- | --- |
| Start of measurement | Onboarding a second restaurant with distinct staff, menu, and table data. |
| End of measurement | Second restaurant operational; cross-restaurant access attempt recorded. |
| Metric | Configuration changes required (count and type); core-code changes required (target **0**); cross-restaurant operations that succeed or leak data (target **0**). |
| Workload & test data | Realistic second-restaurant dataset under P-SCALE; attempted access with a first-restaurant membership against second-restaurant resources. |
| Error / external-delay inclusion | Not latency-relevant. Any core-code change needed to add the second restaurant is a failure of the stated capability (accepted as long as the acceptance boundary is stated). |
| Target | **Zero** cross-restaurant breaches; **zero** core-code changes** required for a second restaurant within the agreed onboarding boundary (D-01/D-02). Accepted as capability; boundary TBD. |
| Status | Approved (invariant) / Partial |

### 2.12 NFR-012 — Adaptive multimedia

| Field | Definition |
| --- | --- |
| Start of measurement | Throttled test connection at a stated profile. |
| End of measurement | Media detail level chosen and rendered (or fallback content shown). |
| Metric | Correct detail level selected per profile; fallback availability (yes/no); completion outcome. |
| Workload & test data | Profiles agreed under D-12/Q-46; throttle levels stated. |
| Error / external-delay inclusion | Fallback is a success only when it matches an agreed profile; otherwise it is a failure. |
| Target | **TBD**. |
| Status | TBD |

## 3. Focus-area call-outs

### 3.1 Reservation response (NFR-001)

Measure at the Tabiora boundary, not inside the service. Include authoritative booking-source time in the end-to-end figure; report source-unavailable separately. Do not claim a target until D-08 records it; use the Proposed evaluation target above only as a **measurement benchmark**, and label it as such in any report.

### 3.2 Waiter-call delivery (NFR-002)

Three distinct events must be reported: **dispatch**, **endpoint receipt**, **human acknowledgement**. Only endpoint receipt satisfies NFR-002 delivery; human acknowledgement is a separate operational metric under D-07. Do not merge them into one “delivered” number, and do not claim “the waiter has been notified” from system dispatch alone (as required by the recorded reliability checks).

### 3.3 Booking/payment availability (NFR-003)

**24/7 operating window is not 100 % uptime.** Report:

- Calendar availability (all minutes in the window).
- Availability excluding approved maintenance.
- Unexpected outages and provider outages, separately.
- `UNKNOWN` payment outcomes, counted as pending financial outcome — never as success.

No numerical availability target is Approved. 99.9 % is not claimed anywhere in this document.

### 3.4 Search and menu latency (NFR-009, NFR-010)

Measure at receipt-to-return boundaries, outcome-aware. Report p95 and p99; report external-lookup time separately from internal processing time. Initial usable menu and full multimedia completion are separate metrics and must be reported separately.

### 3.5 Security (NFR-005, NFR-006)

Security verification is **invariant-based**, not percentile-based. Zero insecure transports carrying sensitive data and zero secrets in logs are the targets; the sensitive-field inventory, protocol configuration, certificate lifecycle, and review criteria remain TBD. Authentication/authorization acceptance is by **denial tests** (unauthenticated, unauthorized, cross-restaurant), not by latency or throughput.

### 3.6 Multi-restaurant access (NFR-006, AC-004, NFR-011)

Every management operation is tested against: unauthenticated, unauthorized role, cross-restaurant membership, dual-role membership in one restaurant, and a different role set in another restaurant. **No role assignment crosses restaurant boundaries.** The acceptance criterion is **zero successful cross-restaurant operations**; the second-restaurant onboarding must require **zero core-code changes** within the agreed boundary.

## 4. What would move a target from Proposed to Approved

For each Proposed evaluation target, approval requires a decision record under the relevant D-ID (D-08 mainly, plus D-02, D-04, D-07, D-12) containing: selected value, rationale, owner, date, observation window, workload, network profile, percentile choice, and affected FR/NFR/AC references. Until then, any report using these numbers must label them **Proposed evaluation target — not approved**, and must not present them in a Final PDF or stakeholder deck as agreed NFRs.

## 5. Status summary

| NFR / AC | Focus area | Target value | Status |
| --- | --- | --- | --- |
| NFR-001 | Reservation response | p95 ≤ 2 s, p99 ≤ 5 s (proposed) | TBD / Proposed |
| NFR-002 | Waiter-call delivery | p95 ≤ 5 s, p99 ≤ 15 s (proposed) | TBD / Proposed |
| NFR-003 | Booking/payment availability | 99.5 % excl. maintenance (proposed); 24/7 ≠ 100 % | TBD / Proposed |
| NFR-004 | No double booking | ≤ 1 CONFIRMED under concurrent conflict | Approved invariant |
| NFR-005 | Encryption | 0 insecure paths; 0 secrets in logs | Approved invariant |
| NFR-006 / AC-004 | Auth / multi-restaurant | 0 cross-restaurant or unauthorized successes | Approved invariant |
| NFR-007 / AC-003 | Mobile usability | 100 % task completion, 0 clipped controls (proposed) | TBD / Proposed |
| NFR-008 | Logging / observability | 100 % traceability of agreed events; 0 secrets | Approved invariant / Partial |
| NFR-009 | Search latency | p95 ≤ 1.5 s, p99 ≤ 3 s (proposed) | TBD / Proposed |
| NFR-010 | Menu latency | initial p95 ≤ 2 s; multimedia p95 ≤ 5 s (proposed) | TBD / Proposed |
| NFR-011 | Multi-restaurant architecture | 0 core-code changes; 0 cross-restaurant breaches | Approved invariant / Partial |
| NFR-012 | Adaptive media | TBD | TBD |
