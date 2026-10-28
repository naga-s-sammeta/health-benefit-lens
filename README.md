# Health Benefit Lens

Healthcare payer analytics across member management, enrollment, and pharmacy
benefit management (PBM).

Health Benefit Lens is a portfolio project that will combine synthetic healthcare payer
data into a governed, queryable layer for executive reporting. It will
demonstrate HIPAA-oriented technical safeguards using entirely invented data.

## Current Status

Day-one project scaffold. The scope, business rules, architecture, and expected
demo results are documented. Application code, databases, authentication, and
dashboards have not been implemented yet.

## Start Here

Read [the day-one guide](docs/day-1.md), then work through the three-member
example. Its expected results will become our first automated tests.

## Business Questions

- How many members have active coverage on a reporting date?
- What is net plan-paid pharmacy spending by service month?
- Which plans account for coverage-matched pharmacy spending?
- Which claims cannot be matched to a member or coverage interval?
- How current is each source's data?

## Planned Stack

| Component | Tool |
|---|---|
| Synthetic sources and ingestion | Python |
| Database | PostgreSQL |
| SQL transformations and model tests | dbt Core |
| Authorized analytics API | FastAPI |
| Executive dashboard | Streamlit |
| Local runtime | Docker Compose |
| Automated checks | GitHub Actions |

## Milestones

- [x] Create a separate project, initial scope, and hand-checkable demo.
- [ ] Generate deterministic synthetic records and expected results.
- [ ] Ingest records with validation, duplicate handling, and quarantine.
- [ ] Build unified models with coverage matching and claim reversals.
- [ ] Add authentication, restricted database roles, and audit events.
- [ ] Build the dashboard and measure end-to-end update latency.
- [ ] Package the local runtime, CI, screenshots, and recorded demo.

## Project Documents

- [Day 1: datasets, metrics, and expected results](docs/day-1.md)
- [Architecture and implementation boundaries](docs/architecture.md)

## Portfolio Delivery

The first release will run locally with reproducible synthetic data. The GitHub
README and portfolio will link a 3-5 minute recorded demonstration. Paid cloud
hosting is optional.

## Data and Compliance Scope

Use synthetic records only. Do not commit credentials, patient information,
employer data, or internal company architecture screenshots. This prototype
does not establish or certify production HIPAA compliance. Document each
implemented safeguard and deployment limitation as development progresses.
