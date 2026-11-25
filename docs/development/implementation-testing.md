# Implementation and Testing Plan

**Status: proposed design-stage plan.** Tabiora has no implemented application, CI/CD, staging evidence, partner contracts, or production environment in this repository. This plan describes work and release conditions; it does not claim that the listed tools, tests, targets, or deployments are already in use. It follows the [architecture baseline](../architecture/architecture-baseline.md), [functional requirements](../requirements/functional-requirements.md), [non-functional requirements](../requirements/non-functional-requirements.md), and [open decisions](../requirements/open-questions.md).


## Implementation Plan

### System Components

These are **logical components**. The [proposed deployment view](../architecture/deployment-operational-view.md) groups the API, core modules, and adapters in one modular backend deployable for a pilot; names such as `PaymentService` do not imply separate microservices. The Payment Provider, Restaurant/POS, and staff notification endpoint remain external.

| Component | Responsibility | Dependencies | Implementation note | Evidence before release |
| --- | --- | --- | --- | --- |
| Customer App | Search and filter restaurants, show floor plan/menu, book a table, prepare a pre-order, pay, call a waiter, and initiate the separate bill-payment flow | Application API; approved mobile browsers | Show conflict, unknown payment, and delivery states honestly; FR-001–FR-011 | Mobile task results and end-to-end traces |
| Staff Interface | Show restaurant-scoped reservations, pre-orders, and waiter calls | Application API; staff identity and membership | P-STAFF role allocation is proposed; no cross-restaurant data | Role and tenant-access tests |
| Application API and access control | Validate request context and enforce customer/staff scope | Core modules; identity mechanism TBD | Recheck restaurant membership on each staff operation; NFR-006 | Unauthorized and cross-restaurant denial traces |
| Discovery and content module | Query restaurant records, menus, floor plans, filters, and availability preview | Data source/content owner D-10; BookingAuthority for authoritative result | Preview is advisory, not a booking commit; FR-001–FR-004/FR-006 | Search/filter and missing-data cases |
| Booking module and `BookingAuthority` port | Validate interval/capacity and coordinate atomic create-if-available | Chosen authority may be Tabiora or Restaurant/POS under D-02 | One confirmed booking for overlapping requests across participating channels; FR-005/NFR-004 | Concurrent and authority-failure cases |
| Pre-order and payment coordinator | Maintain draft/attempt lifecycle, verify provider outcomes, reconcile `UNKNOWN`, and keep bill payment separate | Payment Provider adapter; booking eligibility; quote source D-05 | Never infer success from initiation or timeout; P-PAY proposed | Timeout, duplicate, late-result, cancellation-race cases |
| Restaurant handoff adapter | Send confirmed bookings and eligible paid pre-orders; track `PENDING`/`DELIVERED`/`FAILED` separately | Restaurant/POS contract D-02/D-06 | Preserve business reference on recovery; acknowledgement defines `DELIVERED` | Outage, retry, duplicate and acknowledgement cases |
| Waiter-call module and notifier | Store call state, dispatch to staff endpoint, distinguish receipt from human acknowledgement | Visit-context and routing decisions D-07; staff endpoint | P-CALL proposed; FR-010/FR-014/NFR-002 | Delivery-failure and authorized-acknowledgement cases |
| Data store and recovery task | Persist Tabiora-owned records, correlation references, and pending recovery work | Database engine, topology and backup policy TBD | Booking writes belong here only if D-02 assigns authority to Tabiora; worker separation is TBD | Migration, restore and reconciliation rehearsal |

Optional profile, recommendation, and occupancy capabilities retain their FR-015–FR-026 priorities; they do not block the initial Must Have workflows unless the release scope changes under D-01.

### Implementation sequence and candidate tools

1. **Foundation:** agree the authority, identity, provider, POS, and data contracts; choose a stack; create versioned schema, adapter interfaces, secret handling, and an executable build. Record contract gaps under D-02/D-04–D-07.
2. **Booking increment:** implement discovery, floor-plan preview, booking validation and atomic authority result; exercise `INVALID`, `CONFLICT`, and `SOURCE_UNAVAILABLE` before exposing `CONFIRMED`.
3. **Pre-order/payment increment:** implement draft and quote rules, payment attempt registration, verified callbacks/query, `UNKNOWN` reconciliation, and independent restaurant handoff. Implement FR-011 bill payment as its own workflow after D-05 defines its source and settlement rules.
4. **Restaurant-service increment:** implement restaurant-scoped staff access, waiter-call dispatch, endpoint receipt, acknowledgement, and resolution under an approved role/routing policy.
5. **Hardening:** add test evidence, mobile checks, migration/restore rehearsals, security review, operational signals, and agreed release gates before production approval.

**Candidate tooling:** GitHub for source/review; GitHub Actions for CI; Docker for reproducible packaging; Spring Boot with JUnit, Mockito, and Spring Boot Test if Java is selected; Playwright for browser journeys; Bruno or Postman for API scenarios; JMeter for load measurements; a coverage reporter and static analyzer compatible with the chosen language.

## Deployment Workflow

This is the **repeatable per-change process** proposed for both ordinary changes and a controlled release. The actual CI/CD pipeline remains to be implemented.

| Step | Evidence and stop condition |
| --- | --- |
| 1. Review change | PR links FR/NFR/UC and D-IDs, identifies affected states, migrations and partner contracts, and has a peer review. Unresolved behavior cannot silently become an accepted requirement. |
| 2. Build and check | Reproducible build, static checks, unit and module tests, coverage report, and relevant integration/contract tests pass. Failed critical invariants stop promotion. |
| 3. Deploy to staging | Use versioned artifact and staging-only secrets; apply/rehearse migration on representative data; keep provider/POS simulators visibly labelled. |
| 4. Verify staging | Run affected black-box cases, mobile journeys, fault injection, access checks, and restoration/reconciliation exercises. Partner sandbox evidence is required for enabled real integrations. |
| 5. Approve release | Ernest, as Product Owner, approves behavior and open-business-rule disposition; the technical release owner and team member responsible for testing sign the recorded gate results. These two release responsibilities must be assigned. |
| 6. Controlled production rollout | Follow the milestone plan below, monitor booking conflicts, `UNKNOWN` age, handoff backlog, notification failures, access denials, and latency. Pause on incorrect outcomes. |
| 7. Recover or roll back | Revert compatible application artifact or execute planned forward repair. Reconcile provider/POS effects by stable reference before resuming dispatch; a code rollback never reverses an external charge. |

### Production Deployment Plan

This is the **first real-user release after a milestone**, separate from the repeated workflow. No hosting provider or date is selected.

| Release concern | Required decision or action before launch |
| --- | --- |
| Target and topology | Name the pilot restaurants, region, target environment, network boundaries, browser-facing tier, one backend deployable, data store, and recovery-task placement. Confirm whether booking authority is local or Restaurant/POS (D-01/D-02). |
| Partner readiness | Confirm Payment Provider and Restaurant/POS contracts, signed sandbox/production credentials, callback authenticity, idempotency/acknowledgement semantics, test references, and a support contact (D-02/D-04–D-06). Disable an integration whose contract is not verified. |
| Configuration and secrets | Keep production credentials outside source/images, provide runtime access only to needed components, verify encrypted customer/staff/partner transport, and document rotation and restaurant-specific configuration (NFR-005). |
| Data and migration | Inventory Tabiora-owned records and external references; take a verified backup; rehearse schema migration and restore with representative staging data; choose compatibility window, rollback versus forward repair, retention, RPO and RTO. Values remain TBD. |
| Rollout and ownership | Assign product approver, technical release owner, tester, on-call/restaurant escalation route, release window and go/no-go record. Start with a limited pilot and explicitly decide expansion after observed checks. |
| Verification after switch | Exercise one search, booking, pre-order payment, restaurant handoff, waiter call, and separate bill-payment flow where enabled. Check correct records, external acknowledgement, truthful visible status, logs and alerting. Never use a real charge as an unapproved smoke test. |
| Incident response | Stop affected capability if double booking, false payment success, duplicate charge, unauthorized access, or lost confirmed handoff appears. Preserve references, reconcile external outcomes, restore/repair local state, notify owners, and record restart approval. |

The [Deployment & Operational View](../architecture/deployment-operational-view.md) holds the proposed modular-monolith topology and recovery invariants. Provider, POS, backup, staffing, and availability details need architectural and stakeholder review before a production release.

## Test Plan

### Testing Policies — proposed

| Policy | Required evidence |
| --- | --- |
| Change coverage | Every behavior change links to a requirement/use case and adds or updates a normal, boundary, or failure case. Booking, payment, handoff, waiter notification, and authorization changes include the corresponding invariant regression. |
| Merge gate | Peer review, reproducible build, static checks, relevant unit/module and integration/contract tests, and the coverage policy below pass. A test skipped because an adapter is unavailable is recorded as unverified, not passed. |
| Staging gate | Run end-to-end journeys and fault injection against the release artifact. Simulators verify Tabiora behavior only; they do not establish live provider/POS compatibility. Use partner sandbox or contract evidence for any enabled production integration. |
| Release gate | Do not promote when a critical invariant case fails, an agreed coverage gate fails, a security check fails, or a required partner contract is unverified. Product and technical owners record exceptions with scope, reason, risk, compensating check, owner and expiry; no exception may waive false payment success, double booking, duplicate charge, or cross-restaurant access. |
| Test data and evidence | Use isolated restaurants, tables, memberships, time intervals and synthetic provider references. Reset state between tests. Record artifact revision, environment, mock/partner mode, input, observed output, persisted state, external effect, and result. Redact personal/payment data (NFR-008). |

The test-case template is: **Test ID; Input/preconditions; Expected observable result; Mock/test data; Related FR/NFR; Component.** Each test is executable only after the listed open business decisions and contracts have been fixed for that release. Proposed lifecycle names below refer to P-BOOK/P-PAY/P-CALL/P-INTEGRATION.

| Check before promotion | Candidate tool and concrete use |
| --- | --- |
| Unit/module and branch checks | JUnit/Mockito plus JaCoCo if Java/Spring Boot is selected; exercise booking validation, payment-state guards, handoff deduplication and restaurant-scoped authorization. Other stacks require equivalent tools. |
| Adapter/contract integration | Spring Boot Test if Java is selected, or an equivalent integration runner; validate provider callbacks/query, POS acknowledgement, database migration and staff endpoint receipt against a sandbox or recorded contract. Mocks remain separately labelled. |
| API and browser journeys | Bruno/Postman collections for outcomes and state inspection; Playwright for customer/staff paths, mobile viewport, conflicts and error messages. |
| Performance and reliability | JMeter or an equivalent load runner with saved workload/data/network settings; fault injection for provider timeout, authority failure, POS outage and notification failure. |
| Non-execution review | Peer walkthrough of state changes, requirement/UML consistency, secrets and logs; static analyzer selected with the implementation stack. |

The original black-box capacity example is retained as an **illustrative P-BOOK equivalence partition** for a table that seats four. It is a proposed validation rule, not an approved restaurant policy.

| Equivalence class | Input / precondition | Expected observable result |
| --- | --- | --- |
| Invalid lower boundary | Guest count `0`, capacity `4` | Return `INVALID` for guest count; create no reservation or restaurant handoff. |
| Valid range | Guest count `1` through `4`, table and interval otherwise available | Return `CONFIRMED` with one authoritative reservation reference; record the reservation and start a separately tracked restaurant handoff. |
| Invalid upper boundary | Guest count `5`, capacity `4` | Return `INVALID` for capacity; create no reservation or restaurant handoff. |

### Black-box functional and failure cases

| Test ID | Input / preconditions | Expected observable result | Mock / test data | Related FR/NFR | Component |
| --- | --- | --- | --- | --- | --- |
| TC-DISC-01 | Search with supported dish, price and distance filters; include a no-match query | Every returned restaurant satisfies the selected filters; no match is shown as an empty result, not as a request failure; no booking is created | Seed two matching and one excluded restaurant; filter semantics D-01 | FR-001–FR-003, NFR-009 | Discovery |
| TC-BOOK-01 | Two simultaneous overlapping requests for the same table and interval | At most one request returns `CONFIRMED` with a stored authoritative reference; the other returns `CONFLICT`; no duplicate authoritative booking exists | Same table/interval, two customers, synchronized starts | FR-005, NFR-004 | Booking Authority |
| TC-BOOK-02 | Guest count zero or above table capacity; invalid interval | Request returns `INVALID`, identifies the invalid field, and creates no reservation or restaurant handoff | Capacity 4; counts 0 and 5; reversed interval | FR-005, P-BOOK | Booking |
| TC-BOOK-03 | Booking authority rejects the request as unavailable before a write | Customer sees `SOURCE_UNAVAILABLE`, not `CONFIRMED`; no reservation or restaurant handoff is created | Explicit authority-unavailable response | FR-005, NFR-004 | Booking Authority |
| TC-BOOK-04 | Change an existing reservation to an occupied interval, if FR-020 is enabled | Change is rejected with `CONFLICT`; original reservation remains confirmed and unchanged; no second booking or partial update appears | Existing booking plus overlapping target | FR-020, NFR-004 | Booking |
| TC-BOOK-05 | Authority commits a booking but its reply is lost | Customer sees `SOURCE_UNAVAILABLE` until the original reference is reconciled; the authority has exactly one `CONFIRMED` reservation; no blind retry creates a second booking | Commit succeeded, reply dropped, same reference queried | FR-005, NFR-004 | Booking Authority |
| TC-PAY-01 | Provider times out after pre-order payment initiation | Existing attempt becomes `UNKNOWN`; UI shows pending/unknown, not paid or failed; no new attempt is initiated; reconciliation uses the same reference | Timeout then query/callback simulator | FR-008, NFR-008 | Payment coordinator |
| TC-PAY-02 | Duplicate and out-of-order provider callbacks for one attempt | One verified success establishes `SUCCEEDED`; the order becomes `PAID` only if eligible; no duplicate charge/order/handoff; a later failure cannot regress success | Repeated success and stale failure for same reference | FR-008–FR-009, NFR-008 | Payment coordinator |
| TC-PAY-03 | Customer explicitly cancels an unpaid pre-order before payment initiation | Pre-order becomes `CANCELLED`; no Payment attempt or restaurant order handoff is created; the table reservation remains independent | Eligible `DRAFT`, cancellation committed first | FR-007–FR-008, P-PAY | Pre-order/payment |
| TC-PAY-04 | Verified payment success arrives after visit/order becomes ineligible | Payment remains `SUCCEEDED` as financial truth; pre-order becomes `REVIEW_REQUIRED`; no automatic rebooking, fulfilment, or refund; review is recorded | Late verified callback | FR-008–FR-009, NFR-008 | Payment coordinator |
| TC-PAY-05 | Provider returns a verified rejection for an eligible pre-order | Attempt becomes `FAILED`; no paid confirmation or restaurant order handoff occurs; the eligible order remains `AWAITING_PAYMENT` pending the approved retry/cancellation rule | Verified rejection for one attempt | FR-008, NFR-008 | Payment coordinator |
| TC-PAY-06 | Payment initiation commits before a simultaneous cancellation request | Cancellation is rejected while the attempt is unresolved; the order remains `AWAITING_PAYMENT`, the original attempt remains `INITIATED`, and no second attempt or fulfilment handoff is created | Controlled race, initiation committed first, no provider result yet | FR-007–FR-008, P-PAY | Pre-order/payment |
| TC-POS-01 | POS explicitly rejects a handoff after a confirmed booking or eligible paid order | Booking/payment status is unchanged; delivery becomes `FAILED`; retry uses the original business reference and does not mark `DELIVERED` without acknowledgement | Explicit POS failure, then retry | FR-009, NFR-008 | Handoff adapter |
| TC-POS-02 | POS accepts a send but acknowledgement is delayed | Booking/payment status is unchanged; delivery remains `PENDING`; later agreed acknowledgement changes only delivery to `DELIVERED` for the same reference | Send accepted, acknowledgement delayed then received | FR-009, NFR-008 | Handoff adapter |
| TC-CALL-01 | Valid visit context; dispatch succeeds, then staff endpoint receipt and waiter acknowledgement occur | Call reference is stored; dispatch alone does not show `NOTIFIED`; endpoint receipt records `NOTIFIED`; authorized waiter action records `ACKNOWLEDGED`, then `RESOLVED` separately | Staff endpoint receipt simulator | FR-010, FR-014, NFR-002 | Waiter call |
| TC-CALL-02 | Staff endpoint explicitly reports delivery failure | Call remains stored with `DELIVERY_FAILED`; customer is not told a waiter received it; any reroute keeps the original reference under the agreed policy | Explicit endpoint failure | FR-010, FR-014, NFR-002 | Waiter call |
| TC-CALL-03 | Staff member without the required restaurant role tries to acknowledge a call | Request is denied; call remains in its prior state; no staff assignment is recorded; denial is logged with a redacted reference | Waiter in another restaurant; P-STAFF role set | FR-014, NFR-006 | Waiter call/access control |
| TC-AUTH-01 | Unauthenticated staff request and a staff member acting on another restaurant | Both requests are denied; other restaurant's records are not returned or changed; denial is logged without secrets | Two restaurant memberships; WAITER/MANAGER roles | FR-012–FR-014, NFR-006/NFR-008 | Access control |
| TC-BILL-01 | Customer reviews a restaurant bill and initiates phone payment under the agreed bill-source contract | Displayed amount matches authoritative bill reference; only a verified provider result can show payment success; pre-order prepayment does not mark the bill paid | Separate bill and pre-order references; D-05 contract | FR-011, NFR-008 | Bill payment |

### Glass-box coverage and critical paths

**Proposed, awaiting team approval.** A selected language-compatible coverage reporter measures executable statements/lines and branches in the booking, pre-order/payment, handoff, waiter-call, and authorization modules. Exclude generated code and test fixtures with a published exclusion list. For a future Java implementation, JaCoCo is a candidate reporter. The proposed merge and production gate is **at least 80% statement/line coverage and 70% branch coverage in each affected critical module**, plus 100% passing tests for the critical cases above. These percentages are planning targets, not instructor requirements or measured project results. Revisit them after the first executable baseline; record approval and exact reporter semantics. No arbitrary 100% all-path promise is made.

Path coverage means the following **finite critical alternatives**, checked by named tests and code-path evidence: booking valid/conflict/invalid/authority unavailable; payment verified success/rejection/timeout/duplicate/late success/cancel-initiation race; handoff acknowledgement/outage/retry; call receipt/failure/acknowledgement; authorized/unauthorized/cross-restaurant access. The test owner maps each path to an automated case or a recorded manual execution. An unmeasurable gate at the design stage is marked **not yet measurable** with owner and due milestone; it does not count as passed. Production promotion waits for approved thresholds and evidence.

### Non-functional verification and acceptance

| Requirement | Measurement boundary and workload | Acceptance status |
| --- | --- | --- |
| NFR-001 booking latency | From Tabiora receipt of a valid request to definitive `CONFIRMED`/`CONFLICT`/error response, including authority time. Report each outcome's percentile under a recorded concurrency, dataset and network profile. | Maximum, percentile and workload **TBD under D-08**; no 2-second claim. |
| NFR-002 waiter-call delivery | From stored call creation to receipt at the agreed staff endpoint; record dispatch, endpoint receipt, and human acknowledgement separately, including routing/retry time. | Endpoint, percentile, maximum and offline policy **TBD under D-07/D-08**. |
| NFR-003 availability | Monitor booking and payment probes over the agreed calendar window; separately report planned maintenance and external outages. A valid conflict is a correct response; `UNKNOWN` is not paid. | 24/7 operating intent with scheduled maintenance; numerical uptime target and denominator **TBD under D-08**; no 99.9% claim. |
| NFR-004 no double booking | Concurrent same-table overlapping writes, including each participating channel and a failed authority result; inspect the authoritative record. | Zero pairs of overlapping `CONFIRMED` bookings; cross-channel scope depends on D-02. |
| NFR-005/NFR-006 security | Check encrypted customer/staff/partner paths and deny unauthenticated or cross-restaurant management requests without state change. | No sensitive plaintext transport, no unauthorized data exposure or mutation; exact protocol/identity details **TBD**. |
| NFR-007 mobile | Run discovery, booking, checkout, waiter call and separate bill-payment tasks on the approved device/browser/viewport matrix; verify controls and error messages remain usable. | Matrix and usability criteria **TBD under D-08**. |
| NFR-008 auditability | Trace booking, payment, handoff and denial by redacted correlation reference; inspect logs for secrets/card data. | Required events present and attributable; no raw card data or credentials in logs; retention **TBD**. |
| NFR-009/NFR-010 search/menu time | Measure request to results or usable menu separately from media completion, with agreed filters, data size, concurrency and network profile. | Target and percentile **TBD under D-08**. |

Keep the approved result, actual observed value, test environment, dataset, workload, artifact revision, and sign-off together. The [NFR verification rules](../requirements/non-functional-requirements.md#verification-conditions) define the detailed measurement questions. Until D-08 and partner contracts are resolved, tests can characterize behavior but cannot certify an unapproved numerical service target.

## Team review and handoff

Masha prepares the Development Process and Implementation & Testing sections, including the testing policy, test cases, and release-gate criteria, and passes the reviewed text to Ernest for final PDF assembly. Ernest reviews deployable topology, booking authority, partner adapters, migration/recovery, and production ownership; Zakhar checks NFR targets, workloads, traceability, and risk priority; Nikita checks state transitions and guards against the maintained UML. The team and business representatives still need to settle D-01–D-08 and D-11 where they affect release. [Lab 5 process](methodology.md) and [Lab 12 maintenance source](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.4otosi6950iz) are related plans. This document is ready for technical review, not a claim of team approval.
