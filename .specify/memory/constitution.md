<!--
Sync Impact Report
- Version change: N/A → 1.0.0
- Modified principles: Template placeholders → concrete principles
- Added sections: Core Principles (populated), Data Sources & Modeling Standards, Development Workflow & Quality Gates
- Removed sections: None
- Templates updates:
	- ✅ .specify/templates/plan-template.md (removed broken commands reference)
	- ✅ .specify/templates/spec-template.md (no changes required)
	- ✅ .specify/templates/tasks-template.md (no changes required)
	- ✅ .specify/templates/checklist-template.md (no changes required)
	- ⚠ .specify/templates/commands/* (folder not present; no action)
- Follow-up TODOs:
	- TODO(DATA_LICENSES): Confirm which 3rd-party data sources (if any) are licensed and permitted for use beyond the official FPL API.
-->

# FPL Helper Constitution

## Core Principles

#### CONSTITUTION.DATA_PRINCIPLES
- The system MUST use publicly accessible, legally permitted data sources.
- No scraping behind authentication or unpaid paywalls is allowed.
- All data MUST be traceable to transparent, reputable origins.
- Historical and statistical data MUST be reproducible and documented.

### I. Data Provenance & Integrity (NON-NEGOTIABLE)
- All input data MUST declare source, fetch time (UTC), version/hash, and licensing/terms.
- Only permitted sources MAY be used; scraping that violates a provider’s ToS is PROHIBITED.
- Data ingestion MUST validate schema, types, and ranges; invalid records are rejected and logged.
- Player and team identities MUST be stable and joined using canonical IDs (e.g., FPL `element` and team IDs).
- Every dataset MUST include last-updated metadata; downstream computations MUST surface their input timestamp.

Rationale: Accurate recommendations require trustworthy, traceable data with clear lineage and compliance.

### II. Reproducible Analytics & Test-First
- Scoring models, projections, and optimization logic MUST be deterministic given the same inputs and seed.
- Unit tests MUST cover all metric computations (e.g., expected points, risk scores, fixture difficulty).
- Backtests MUST fix data snapshots and seeds; results are compared against stored baselines.
- Contract tests MUST validate parsing of upstream payloads against sample fixtures (teams, players, fixtures, injuries).
- Any data transformation MUST be accompanied by tests asserting invariants (e.g., minutes ≥ 0, sum of positions in squad constraints).

Rationale: Determinism and tests enable safe iteration, debugging, and confidence in model changes.

### III. Transparent Recommendations & Explainability
- Every recommendation MUST provide an explanation payload: key drivers, features used, weights/assumptions, and confidence.
- Output MUST include primary metrics (e.g., expected points), uncertainty/risk, upcoming fixtures context, and data freshness.
- No “black box” outputs: users MUST be able to see why a player/team is suggested.
- Any heuristic or override MUST be documented and feature-flagged.

Rationale: Users make better decisions when the system clearly explains its reasoning and limits.

### IV. Privacy, Terms & Fair Use
- Do not store personally identifiable information beyond what is strictly necessary for functionality.
- Any authentication tokens or user data MUST be encrypted at rest and in transit; secrets stored via secure vault/config.
- Data retention MUST be documented; provide delete/export on request where applicable.
- Comply with Premier League/FPL terms and any third-party data licenses; include a clear disclaimer of non-affiliation.

Rationale: Respect users and rights-holders while keeping the tool sustainable and compliant.

### V. Performance, Freshness & Observability
- API responses for interactive endpoints SHOULD meet p95 < 500ms under expected load; background jobs MUST avoid blocking UX.
- Data freshness targets: official FPL updates reflected within 10 minutes; third-party feeds per their SLAs.
- Structured logs, metrics, and health checks are REQUIRED; alert on ingestion failures and stale datasets.
- Caching MUST publish TTLs and invalidation strategy; avoid serving stale data beyond declared freshness.

Rationale: Timely, observable systems deliver trustworthy, useful guidance to users when it matters.

## Data Sources & Modeling Standards

- Primary sources: Official FPL API (fixtures, players, teams, events). Any additional providers REQUIRE license review (see TODO(DATA_LICENSES)).
- Core entities: Team, Player, Fixture, Injury/Availability, Ownership, Price, Position, Expected Metrics (xG/xA if licensed).
- Keys & joins: Use FPL `element` for players and official team IDs; never rely on names for joins.
- Time & locale: All timestamps in UTC; store and display with league-relevant context as needed.
- Rate limits & retries: Respect provider limits; implement exponential backoff and jitter; surface fetch errors to observability.
- Caching: Publish TTL per dataset; include `as_of` timestamp on all derived outputs.
- Schema management: Version schemas for inputs and outputs; changes require contract tests and a documented migration.

## Development Workflow & Quality Gates

- Constitution Check: Each plan/spec MUST list how principles I–V are satisfied or why exceptions are justified.
- Tests: New analytics or changes MUST include unit tests; integration/contract tests required for data contracts.
- Reviews: PRs MUST include evidence of passing tests and Constitution Check; explainability output MUST be demoed for new recs.
- Breaking changes: Any change to public contracts (APIs, exported data, CLI formats) MUST follow semantic versioning and include migration notes.
- Security: Secrets NEVER committed; CI/CD MUST scan for secrets; dependency updates follow risk-aware review.

## Governance

- Authority: This constitution governs engineering practices for the FPL Helper project and supersedes conflicting conventions.
- Amendments: Proposals submitted via PR with a migration plan where applicable; require maintainer approval.
- Versioning Policy: Semantic versioning for this constitution (MAJOR.MINOR.PATCH).
	- MAJOR: Incompatible governance changes or principle removals/redefinitions.
	- MINOR: New principles/sections or materially expanded guidance.
	- PATCH: Clarifications and non-semantic refinements.
- Compliance: Feature plans/specs MUST include a Constitution Check section; reviewers verify adherence before merge.
- Review Cadence: At minimum quarterly, or after material data-source/league rules changes.

**Version**: 1.2.0 | **Ratified**: 2025-12-03 | **Last Amended**: 2025-12-03
