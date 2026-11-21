# Deployment & Operational View

## Proposed architecture

Use a browser-accessible web tier for the Customer App and Staff Interface. The proposed backend is a **modular monolith** that packages the Application API, use-case modules, and integration adapters in one deployable. A relational database would hold Tabiora-owned records, attempt and business references, and recovery state. The booking write belongs there only if D-02 selects Tabiora as authority. A background recovery task can run with the backend or in a separate worker. Hosting, database engine, frontend packaging, worker isolation, replication, network placement, and deployment tooling remain TBD. [Architecture Map](architecture-map.md)

### Why a modular monolith for the pilot

P-SCALE illustrates a single-region pilot, with demand and quality targets open under D-08. One backend simplifies releases and rollback. Local transactions can coordinate verified payment with follow-up work or enforce the atomic booking rule if Tabiora owns booking. Provider and POS effects still cross trust and transaction boundaries. [Pilot assumptions](../overview.md#target-businesses-and-planning-scale) · [ADR-001](adr/adr-001-authoritative-booking.md) · [ADR-002](adr/adr-002-verified-payment-and-restaurant-handoff.md)

Booking, order, payment, and call modules expose application contracts. Adapters isolate partner protocols. Shared scaling and release cycles also couple failures between modules. Consider extraction if evidence shows sustained independent scaling demand, recurring cross-module failures, or incompatible release cadences. Any extraction must preserve ownership, interfaces, and recovery invariants.

Separate staging and release configuration and credentials. Supply secrets at runtime. Track endpoint and restaurant configuration. Encrypt sensitive traffic and keep credentials and raw card data out of browser code and logs. Test provider, POS, and notification adapters against agreed contracts before enabling them.

## Release, recovery, and rollback

| Situation | Proposed operational behavior |
| --- | --- |
| Schema or configuration change | Validate migration on representative staging data and rehearse a verified restore. Preserve compatibility with the previous application version through the rollback window where feasible. Version application, schema, and non-secret configuration together for release review. For an incompatible change, plan a forward repair or controlled data recovery before deployment. Backup schedule, retention, recovery point, and recovery time remain TBD. |
| Booking authority result missing | Return `SOURCE_UNAVAILABLE` when Tabiora cannot obtain an authoritative result. Preserve existing confirmed reservations. A lost reply can leave the write outcome uncertain. Investigate at the authority before presenting a confirmation or repeating an uncertain operation. Follow D-02 for any external authority contract. |
| Provider timeout or lost callback | Record `UNKNOWN` when a terminal outcome cannot be verified. Query or replay verification for the same attempt reference. Apply verified effects without duplicates, preserve financial truth, and send ineligible or unresolved cases to authorized review. Provider contract, review ownership, and refunds remain D-04. |
| POS unavailable or acknowledgement delayed | Keep handoff in `PENDING` or `FAILED` as appropriate under P-INTEGRATION. Retry or reconcile using the original business reference. Mark `DELIVERED` only on agreed acknowledgement. Retry bounds and escalation remain D-06. |
| Waiter endpoint fails or staff do not respond | Track missing receipt separately from human response. Preserve the call and its reference for any permitted reroute. Routing, no-response handling, and review ownership remain D-07. |
| Application rollback or database restore | Stop or coordinate affected dispatch and reconciliation while recovering. A code rollback cannot reverse a confirmed provider charge or restaurant acceptance. Restore local records to a verified point, reconcile external outcomes using stable references, and verify pending work before resuming dispatch. Escalate mismatches for authorized resolution under D-04/D-06. |

## Operational signals

Use redacted correlation references (NFR-008). Track booking conflicts separately from authority errors, age of `UNKNOWN` payments, handoff backlog and acknowledgement age, failed or unacknowledged waiter calls, latency, and access denials. Validate that each signal supports investigation. Workload, alerts, and numerical targets remain D-08. See [NFR-003 measurement rules](../requirements/non-functional-requirements.md#availability-operating-hours-versus-measured-service).

## Staging release gates

| Gate | Required evidence before the affected release capability |
| --- | --- |
| Booking contract | Parallel same-table overlapping writes preserve NFR-004. Include each participating channel where supported and inject authority failure. [ADR-001](adr/adr-001-authoritative-booking.md) |
| Payments and handoff | Inject provider timeout, missing, duplicate and out-of-order callbacks, and interruption between payment recording and handoff. Inject POS delay or outage, replay with the same reference, and verify separate acknowledgement and financial outcomes. [ADR-002](adr/adr-002-verified-payment-and-restaurant-handoff.md) |
| Staff access and service | Verify receipt, human acknowledgement, and resolution as separate events. Inject notification failure and attempt an operation from an unrelated restaurant. Check role and tenant scope. [Waiter-call sequence](sequence-diagram.md#waiter-call) |
| Release and recovery | Rehearse migration, application rollback or forward repair, backup restoration, and reconciliation with a provider/POS test environment. Verify rollback preserves external outcomes and resumes pending work without duplicate effects. Label mock partner results and keep them distinguishable from partner contract evidence. [Lab 11 Deployment Plan](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.r75t8oa0l1gw#heading=h.botkhs47kfcb) |

## Implemented / verified

Implementation, staging, partner contracts, recovery rehearsals, and production evidence remain pending. Lab tooling, deployment steps, and maintenance are proposals. [Lab 11 Test Plan](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.r75t8oa0l1gw#heading=h.suljx3hzzq1j) · [Lab 12 Maintenance Plan](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.4otosi6950iz)
