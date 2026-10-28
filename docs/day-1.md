# Health Benefit Lens: Day 1

Status: planning brief; no payer pipeline has been implemented yet.

Health Benefit Lens is an independent portfolio project for member, enrollment, and
pharmacy analytics. Its files, dependencies, and implementation live in its own
project directory.

## Today's Outcome

Understand the three source datasets, agree on the first dashboard questions,
and verify a tiny example by hand before generating a larger dataset.

Suggested time: 60-90 minutes for this first session.

## Project Goal

Combine synthetic member, enrollment, and pharmacy claim events into a single
queryable analytics layer. Show enrollment and pharmacy spending, expose data
exceptions, and demonstrate access restrictions and audit events.

The portfolio description will identify this as a synthetic-data prototype
demonstrating HIPAA-oriented technical safeguards, not a production compliance
certification.

## Version 1 Decisions

- Run locally with Python, PostgreSQL, dbt Core, FastAPI, and Streamlit.
- Package the runnable services with Docker Compose.
- Generate all example data; do not use employer or patient data.
- Use a fixed synthetic reporting date: February 15, 2026.
- Target dashboard updates within 60 seconds of receiving an event under the
  demo workload; measure the result before claiming it is achieved.
- Start with three members and three claims, then scale to 1,000 members and
  approximately 10,000 claims after the small example passes.
- Keep cloud hosting, Snowflake, Kafka, and an AI assistant as later extensions.

## The Three Datasets

| Dataset | One row represents | Initial fields |
|---|---|---|
| Members | One synthetic person | member_key, source_member_id, synthetic_name |
| Enrollment | One coverage interval | enrollment_id, member_key, plan_id, coverage_start, coverage_end |
| Pharmacy events | One payment or reversal event | event_id, source_member_id, claim_id, event_type, original_claim_id, service_date, plan_paid_amount, received_at |

Add a crosswalk of source_system, source_member_id, and member_key because source
IDs may differ. Use explicit mappings initially; do not guess identity matches.

Coverage start and end dates are inclusive. A null end date means no known end.
Represent money using decimal values, not floating point. Payment and reversal
amounts are positive in the input; the event type determines the ledger sign.
A reversal references an existing payment and cancels it once. Additional
reversals of the same payment are exceptions in this simplified model.

## First Dashboard Questions

1. How many distinct members have active coverage on the reporting date?
2. What is net plan-paid pharmacy spending for February by service date?
3. How much spending matches recorded coverage, and how much is an exception?
4. Which plans account for coverage-matched pharmacy spending?
5. When was each source last received and successfully processed?

Net spending includes all accepted payment and reversal events, even when
coverage cannot be matched. Report unmatched amounts separately instead of
silently excluding them. Attribute spending to a plan only when the claim
matches exactly one coverage interval on its service date. Overlapping
intervals must produce an exception rather than duplicate the claim.

## Tiny Example: Expected Answers

All dates and identities below are invented.

| Member | Plan | Coverage start | Coverage end |
|---|---|---|---|
| M001 | Plan A | 2026-01-01 | 2026-12-31 |
| M002 | Plan B | 2026-02-01 | 2026-12-31 |
| M003 | Plan A | 2026-01-01 | 2026-01-31 |

| Event | Type | Claim | Member after crosswalk | Service date | Amount |
|---|---|---|---|---|---|
| E001 | Payment | C001 | M001 | 2026-02-10 | 40.00 |
| E002 | Payment | C002 | M002 | 2026-02-11 | 60.00 |
| E003 | Payment | C003 | M003 | 2026-02-12 | 100.00 |

Verify these results in order:

| Action | Active members on Feb 15 | Net spending | Coverage-matched spending | Outside-coverage spending |
|---|---|---|---|---|
| Load the initial records | 2 | 200.00 | 100.00 | 100.00 |
| Replay E002 with the same event ID | 2 | 200.00 | 100.00 | 100.00 |
| Receive E004 reversing C002 for 60.00 | 2 | 140.00 | 40.00 | 100.00 |
| Correct M003's coverage end to Feb 28 | 3 | 140.00 | 140.00 | 0.00 |

E004 uses C002's service date and references C002 through original_claim_id.
The correction updates M003's existing enrollment record by enrollment_id.
Keep the original raw records for traceability and rebuild affected results.
These reporting results reflect the latest accepted corrections, not an
as-known-at-the-time historical report.

## Today's Checklist

- [ ] Read the dataset definitions and explain how the three domains join.
- [ ] Calculate the tiny example and confirm the expected results.
- [ ] Confirm Python and Docker availability on the development machine.
- [ ] Be able to explain the five dashboard questions without reading SQL.

## Next Implementation Milestone

Build a deterministic Python generator that writes the small fixture, member
crosswalk, and expected results. Test the fixture before adding the larger
synthetic dataset. Keep all dependencies and generated files within the
Health Benefit Lens project.

Today does not require a cloud account, credit card, real data, or paid service.
