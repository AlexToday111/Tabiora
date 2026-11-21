# Deployment & Operational View

## Proposed architecture

Use a browser-accessible web tier for the Customer App and Staff Interface. The proposed backend is a **modular monolith** that packages the Application API, use-case modules, and integration adapters in one deployable. A relational database would hold Tabiora-owned records, attempt and business references, and recovery state. The booking write belongs there only if D-02 selects Tabiora as authority. A background recovery task can run with the backend or in a separate worker. Hosting, database engine, frontend packaging, worker isolation, replication, network placement, and deployment tooling remain TBD. [Architecture Map](architecture-map.md)

### Why a modular monolith for the pilot

P-SCALE illustrates a single-region pilot for table-service restaurants. Demand and quality targets remain open under D-08. One backend gives the team one release, rollback, and operational surface. In-process calls and transactions over Tabiora-owned records simplify coordination of local state, such as recording a verified payment result with its follow-up work. If Tabiora owns booking, the same approach can enforce the atomic table/interval decision in the store. External payment and POS actions still require verification, acknowledgement, or reconciliation across their own transaction boundaries. [Pilot assumptions](../overview.md#target-businesses-and-planning-scale) · [ADR-001](adr/adr-001-authoritative-booking.md) · [ADR-002](adr/adr-002-verified-payment-and-restaurant-handoff.md)

Booking, ordering, payments, and waiter calls retain explicit application contracts. Adapters implement the corresponding ports and contain partner-specific protocols. The backend release and scaling unit is shared, so a slow or failing module can affect other workflows and changes share a deployment cycle. Reconsider service extraction if measurements show a sustained independent scaling need, repeated integration failures with cross-module impact, or a release cadence that the shared deployable cannot support. Extract only where the resulting interface, data ownership, and recovery behavior can preserve the existing invariants.

Keep staging configuration and partner credentials separate from release environments. Supply provider verification material, POS credentials, and other secrets to the backend at runtime through the chosen secret store. Keep non-secret endpoints and per-restaurant integration configuration versioned or traceable. Use encrypted connections for sensitive traffic and keep provider credentials and raw card data out of browser code and diagnostic records. Treat the Payment Provider, Restaurant/POS, and notification endpoint as external trust and failure boundaries. Test adapters against agreed contracts before enabling the corresponding integration.

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

Use redacted correlation references for important transitions under NFR-008. Proposed signals include normal booking conflicts and authority errors as distinct counts, age of payment attempts in `UNKNOWN`, handoff backlog and acknowledgement age, waiter-call delivery failures and unacknowledged calls, request latency, and denied or cross-restaurant access attempts. Validate that each signal identifies a scenario the team can investigate. Alert thresholds, workload, measurement conditions, and numerical service targets remain open under D-08. NFR-003 records a 24/7 booking and payment operating window with scheduled maintenance. It provides no measured availability percentage.

## Staging release gates

| Gate | Required evidence before the affected release capability |
| --- | --- |
| Booking contract | Parallel same-table overlapping writes preserve NFR-004. Include each participating channel where supported and inject authority failure. [ADR-001](adr/adr-001-authoritative-booking.md) |
| Payments and handoff | Inject provider timeout, missing, duplicate and out-of-order callbacks, and interruption between payment recording and handoff. Inject POS delay or outage, replay with the same reference, and verify separate acknowledgement and financial outcomes. [ADR-002](adr/adr-002-verified-payment-and-restaurant-handoff.md) |
| Staff access and service | Verify receipt, human acknowledgement, and resolution as separate events. Inject notification failure and attempt an operation from an unrelated restaurant. Check role and tenant scope. [Waiter-call sequence](sequence-diagram.md#waiter-call) |
| Release and recovery | Rehearse migration, application rollback or forward repair, backup restoration, and reconciliation with a provider/POS test environment. Verify rollback preserves external outcomes and resumes pending work without duplicate effects. Label mock partner results and keep them distinguishable from partner contract evidence. [Lab 11 Deployment Plan](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.r75t8oa0l1gw#heading=h.botkhs47kfcb) |

## Implemented / verified

The repository provides design documents and editable models. Implementation, staging deployment, partner contracts, migration and restore rehearsals, and production service evidence are pending. Lab 11 tooling and pipeline steps are proposals. Lab 12 describes planned maintenance and support. [Lab 11 Test Plan](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.r75t8oa0l1gw#heading=h.suljx3hzzq1j) · [Lab 12 Maintenance Plan](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.4otosi6950iz)
