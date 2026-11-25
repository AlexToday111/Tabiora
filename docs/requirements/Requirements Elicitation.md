# Requirements Elicitation

## Audit summary

- **Duplicates / near-duplicates:** Q-01 and Q-13 are related but not duplicates: Q-01 is the first-release problem, Q-13 is first-release capability scope. Q-02 and Q-15 are related but distinct: target restaurant type vs supported operations. Q-19 and Q-32 overlap on payment/refund services; Q-19 must be split and Q-32 kept as refund policy. Q-24–Q-25 and Q-26–Q-27 are parallel reservation/order modification questions, not duplicates.
- **Compound questions to split:** Q-15, Q-18, Q-19, Q-33, Q-37, Q-38, Q-45.
- **Premature solution imposition to rephrase:** Q-17 should not presuppose a “separate management interface”; Q-20 should ask about authoritative coordination, not force a named system owner; Q-36 should ask what evidence is acceptable, not “how the system establishes” presence.
- **Status rule:** keep **Open** unless a real answer is recorded with owner, date, rationale, and affected references.

## Business goals

| ID | Revised question (Open) | Original / notes | Who should answer | Decision needed |
| --- | --- | --- | --- | --- |
| Q-01 | Which customer problem should the first release solve first, expressed as an outcome rather than a feature? | Q-01; not duplicate of Q-13 | Product owner / business sponsor | D-01 |
| Q-02 | Which types of table-service restaurant should the first release serve? | Q-02; related to Q-15 but distinct | Business sponsor + restaurant owner/operator | D-01 |
| Q-03 | Which geographical area should the first release cover? | Q-03 | Business sponsor | D-01 |
| Q-04 | How many restaurants are expected at launch? | Q-04; planning input, not a forecast unless approved | Business sponsor | D-01 |
| Q-05 | How many customers are expected to use the first release? | Q-05 | Business sponsor | D-01 |
| Q-10 | When should the first version launch? | Q-10 | Business sponsor | D-01 |
| Q-11 | What budget is available? | Q-11 | Business sponsor | D-01 |
| Q-12 | How should functionality be introduced over successive releases? | Q-12 | Product owner | D-01 |
| Q-13 | Which recorded capabilities are essential for the first usable release? | Q-13; related to Q-01 but not duplicate | Product owner + business sponsor | D-01 |

## Stakeholders

| ID | Revised question (Open) | Original / notes | Who should answer | Decision needed |
| --- | --- | --- | --- | --- |
| Q-33a | Which reservation actions may a WAITER perform, and which require a MANAGER? | Q-33 split; avoid broad “roles” question | Restaurant manager + owner/operator | D-11 |
| Q-33b | Which pre-order actions may a WAITER perform, and which require a MANAGER? | Q-33 split | Restaurant manager + owner/operator | D-11 |
| Q-33c | Which menu, table-availability, and waiter-call actions require explicit authorization? | Q-33 split | Restaurant manager + owner/operator | D-11 |
| Q-34 | Which staff members should receive a customer waiter call for a given restaurant/service context? | Q-34 | Restaurant manager | D-07 |
| Q-35 | What should happen when no staff member responds to a waiter call? | Q-35 | Restaurant manager + operations lead | D-07 |
| Q-47 | How long should customer activity and preference information be retained, and who may access it? | Q-47; compound retention/access but acceptable if answered separately | Security/compliance + product owner | D-09 |
| Q-48 | Who should investigate a payment whose outcome is still unknown? | Q-48 | Operations lead + payment provider contact | D-04 |

## Functional scope

| ID | Revised question (Open) | Original / notes | Who should answer | Decision needed |
| --- | --- | --- | --- | --- |
| Q-14 | Which restaurant filters do customers need for the first release, beyond dish, price, and distance? | Q-14 | Customer representative + product owner | D-01 |
| Q-15a | Which restaurant-side reservation operations should Tabiora support? | Q-15 split | Restaurant owner/operator | D-02 |
| Q-15b | Which restaurant-side pre-order operations should Tabiora support? | Q-15 split | Restaurant owner/operator | D-02 |
| Q-15c | Which restaurant-side table-availability operations should Tabiora support? | Q-15 split | Restaurant owner/operator | D-02 |
| Q-17 | Which staff tasks require management access, and from which devices or contexts? | Q-17 rephrased to avoid presupposing a separate interface | Restaurant manager + staff | D-02 |
| Q-18 | What accounting functionality, if any, is expected from Tabiora? | Q-18; broad but single topic | Restaurant owner/operator + finance representative | D-02 |
| Q-24 | Which details of a confirmed reservation may a customer change? | Q-24 | Restaurant manager + customer representative | D-03 |
| Q-25 | Until when may a customer change a reservation? | Q-25 | Restaurant manager | D-03 |
| Q-26 | Which details of a confirmed pre-order may a customer change? | Q-26 | Restaurant manager + customer representative | D-03 |
| Q-27 | Until when may a customer change a pre-order? | Q-27 | Restaurant manager | D-03 |
| Q-28 | When may a customer cancel a visit? | Q-28 | Restaurant manager | D-03 |
| Q-29 | How long should an unfinished pre-order remain available for checkout? | Q-29 | Product owner + payment provider | D-04 |
| Q-30 | What should happen to the table reservation when a customer cancels an unpaid pre-order? | Q-30 | Restaurant manager + product owner | D-03 |
| Q-37a | How should favorites work if this capability is selected? | Q-37 split | Customer representative + product owner | D-09 |
| Q-37b | How should feedback/ratings work if this capability is selected? | Q-37 split | Customer representative + restaurant manager | D-09 |
| Q-38 | How should additional in-restaurant orders be handled if this capability is selected? | Q-38; broad but single capability | Restaurant manager + waiter | D-09 |

## Integration

| ID | Revised question (Open) | Original / notes | Who should answer | Decision needed |
| --- | --- | --- | --- | --- |
| Q-16 | Which existing restaurant systems must Tabiora connect to? | Q-16 | Restaurant owner/operator + POS provider | D-02 |
| Q-19a | Which payment methods must be supported for prepayment? | Q-19 split | Payment provider + product owner | D-04 |
| Q-19b | Which payment methods must be supported for bill payment? | Q-19 split | Payment provider + restaurant owner/operator | D-04 |
| Q-19c | Which payment-related services are expected beyond initiation, such as verification or reconciliation? | Q-19 split | Payment provider + operations lead | D-04 |
| Q-20 | What authoritative coordination mechanism must ensure that all booking channels cannot create overlapping confirmed bookings for one table? | Q-20 rephrased to avoid prematurely naming a system owner | Architecture owner + POS provider + restaurant operator | D-02 |
| Q-21 | Who maintains restaurant, menu, price, and floor-plan information? | Q-21 | Restaurant owner/operator + POS provider | D-10 |
| Q-22 | Which system provides the restaurant bill amount, and who owns bill adjustments? | Q-22 | Restaurant owner/operator + POS provider | D-05 |
| Q-23 | How should staff handle confirmed information that has not reached the restaurant? | Q-23 | Restaurant manager + operations lead | D-06 |

## Constraints

| ID | Revised question (Open) | Original / notes | Who should answer | Decision needed |
| --- | --- | --- | --- | --- |
| Q-45a | Which mobile platforms must be supported? | Q-45 split | Product owner + QA | D-08 |
| Q-45b | Which browser versions must be supported? | Q-45 split | Product owner + QA | D-08 |
| Q-46 | What menu content should remain usable on a slow connection? | Q-46 | Product owner + QA | D-12 |
| Q-47 | How long should customer activity and preference information be retained, and who may access it? | Q-47; also listed under stakeholders | Security/compliance + product owner | D-09 |

## Performance

| ID | Revised question (Open) | Original / notes | Who should answer | Decision needed |
| --- | --- | --- | --- | --- |
| Q-06 | How many customers and staff are expected to use the system at the same time? | Q-06 | Business sponsor + operations lead | D-08 |
| Q-07 | How many reservation requests are expected per day? | Q-07 | Business sponsor | D-08 |
| Q-08 | How many pre-orders are expected per day? | Q-08 | Business sponsor | D-08 |
| Q-09 | What demand is expected during the busiest periods? | Q-09 | Business sponsor + operations lead | D-08 |
| Q-39 | How long may a customer wait for a reservation result? | Q-39 | Product owner + operations lead | D-08 |
| Q-40 | How long may a customer wait for search results? | Q-40 | Product owner | D-08 |
| Q-41 | How long may a customer wait for a usable menu? | Q-41 | Product owner + QA | D-08 |
| Q-42 | How quickly should staff receive a waiter-call notification? | Q-42 | Restaurant manager + operations lead | D-08 |
| Q-43 | How much unexpected service interruption is acceptable? | Q-43; availability, not pure performance | Operations lead + product owner | D-08 |

## Security

| ID | Revised question (Open) | Original / notes | Who should answer | Decision needed |
| --- | --- | --- | --- | --- |
| Q-33c | Which menu, table-availability, and waiter-call actions require explicit authorization? | Also stakeholder/operation; security-relevant | Restaurant manager + security/compliance | D-11 |
| Q-47 | How long should customer activity and preference information be retained, and who may access it? | Security/privacy relevant | Security/compliance + product owner | D-09 |
| Q-48 | Who should investigate a payment whose outcome is still unknown? | Financial/security ownership | Operations lead + payment provider contact | D-04 |

## Operation

| ID | Revised question (Open) | Original / notes | Who should answer | Decision needed |
| --- | --- | --- | --- | --- |
| Q-31 | How should a received payment be handled after the visit or order is no longer valid? | Q-31 | Restaurant manager + payment provider | D-04 |
| Q-32 | Under what circumstances should a payment be refunded? | Q-32; related to Q-19 but distinct | Restaurant owner/operator + payment provider | D-04 |
| Q-36 | What evidence is acceptable to confirm that a customer is on premises for a waiter call or bill payment? | Q-36 rephrased to avoid prematurely prescribing mechanism | Restaurant manager + security/compliance | D-07 |
| Q-44 | When may scheduled maintenance interrupt service? | Q-44 | Operations lead | D-08 |
| Q-48 | Who should investigate a payment whose outcome is still unknown? | Also listed under security/stakeholders | Operations lead + payment provider contact | D-04 |

