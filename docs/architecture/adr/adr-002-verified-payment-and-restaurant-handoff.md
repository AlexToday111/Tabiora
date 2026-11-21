# ADR-002 Verified Payment and Recoverable Restaurant Handoff

## Status

**Proposed.** Provider selection, checkout, refunds and review ownership remain open under D-04. Bill source and settlement remain open under D-05. Acknowledgement, retry policy and escalation remain open under D-06.

## Context / ASR

FR-008 covers prepayment, FR-011 covers bill payment, and FR-009 covers restaurant handoff. NFR-005 and NFR-008 require encrypted transport and operation logging. AC-001/AC-005 govern restaurant integration. AC-002/AC-006 recommend delegated card processing and isolated integrations. Callbacks can be delayed, duplicated, lost, or out of order. Restaurant outages can follow booking or payment confirmation.

## Decision

- **Verified payment.** `PaymentService` initiates, verifies, and queries through `PaymentProviderPort`. Carry a stable attempt reference and use it for provider idempotency where supported. Keep provider references and necessary metadata. Raw card handling stays with the external Payment Provider. Only a verified terminal result can establish `SUCCEEDED` or `FAILED`. A timeout or missing trustworthy terminal result yields `UNKNOWN`. Reconcile that attempt through `PaymentService` before allowing a fresh one. Duplicate or out-of-order results may resume unfinished work but must preserve `SUCCEEDED` and avoid duplicate effects.
- **Pre-order eligibility.** Recheck the quote, order, and reservation at initiation and check current eligibility when applying the result. A late verified success for an ineligible order or visit records `Payment.SUCCEEDED` and routes the `PreOrder` to `REVIEW_REQUIRED`. The booking lifecycle continues independently. There is no automatic rebooking, fulfilment, or refund. FR-011 and UC-06 retain a separate bill workflow.
- **Restaurant handoff.** Track confirmed reservations and eligible paid pre-orders through `POSIntegration` with independent `PENDING`, `DELIVERED`, and `FAILED` states. A successful send leaves acknowledgement pending. Verify any partner response against the agreed acknowledgement contract before recording `DELIVERED`. Retain the original business reference for recovery. A durable outbox and retry worker are candidate mechanisms. `StaffNotifier` receipt and human waiter acknowledgement follow P-CALL separately.

## Alternatives

- **Handle card details inside Tabiora.** Gives more control over processing and expands sensitive data handling beyond the boundary recommended by AC-002.
- **Use a synchronous payment-to-POS chain and mark delivery on send.** Simplifies the happy path, but external downtime can obscure which action succeeded and cause duplicate or lost effects on retry.

## Consequences

- **Positive.** Financial truth survives timeout and restaurant outages. Separate records make recovery and important transitions auditable. Adapters isolate provider and POS behavior.
- **Negative.** Handoff can lag confirmation. Reconciliation, duplicate suppression, and recoverable state updates add complexity. An authorized review owner and a restaurant escalation path are needed for unresolved cases.

## Links / Verification

Inject timeouts, untrusted results, and lost callbacks. Check visible `UNKNOWN` and same-reference reconciliation. Test duplicate and out-of-order results, late success after expiry, and interruption before handoff. Simulate a POS outage and replay the same business reference. Preserve payment and reservation outcomes, suppress duplicate effects, and require acknowledgement for `DELIVERED`.

[Original Lab 9 ADR-002](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.7qbrdid5xjwc#heading=h.zfsils3t0ksz) · [Architecture Map](../architecture-map.md) · [Ordering model](../class-diagram.md#ordering-and-payment) · [Payment sequence](../sequence-diagram.md#payment-initiation-and-outcomes) · [Canonical lifecycle](../../requirements/functional-requirements.md#proposed-lifecycle-model) · [UC-06](../../requirements/use-cases.md#uc-06--pay-the-restaurant-bill) · [Reliability checks](../../requirements/non-functional-requirements.md#proposed-reliability-checks) · [Lab 11 Test Plan](https://docs.google.com/document/d/1uYLRcOJxa0YR9Lsh4C2f--5P2zQ14fUCAJ1MeqVXjpA/edit?tab=t.r75t8oa0l1gw#heading=h.suljx3hzzq1j)
