# KeyVa Completion Plan

Date: 2026-04-06 (UTC)

## Context

This workspace does not contain the `KeyVa` source tree, so implementation work cannot be done directly here.

To still move the project forward, this document defines a concrete, execution-ready roadmap to take KeyVa from prototype to production-ready MVP.

## Outcome goals

- Users can register, log in, recover accounts, and manage sessions safely.
- Users can fund/use wallets and create payment links reliably.
- Transactions are consistent, auditable, and resilient under load/failures.
- Deployment, observability, and incident response are operational.

## Phase 1 — Security and platform baseline (Week 1)

### Tasks

1. Replace any development fallback secrets with required environment variables.
2. Add request validation (schema-driven) on all auth, link, and transaction endpoints.
3. Add auth protection middleware and role/ownership checks where applicable.
4. Add CSRF protection for session-authenticated mutations.
5. Add auth rate limiting and login lockout/backoff.
6. Enforce secure cookie/session config for production.
7. Add `helmet` + strict CORS policy.

### Acceptance criteria

- App fails fast when required env vars are missing.
- All POST/PATCH payloads are validated and return structured errors.
- Security headers are present in responses.
- Brute-force test confirms limits and lockout behavior.

## Phase 2 — Recovery and account lifecycle (Week 1–2)

### Tasks

1. Implement forgot-password endpoint:
   - Generates one-time reset token.
   - Stores hashed token + expiry.
2. Implement reset-password endpoint:
   - Validates token and expiry.
   - Rotates password and invalidates all outstanding reset tokens.
3. Integrate email delivery provider (or stub in local/dev mode).
4. Wire Recovery UI pages to real backend endpoints.

### Acceptance criteria

- End-to-end password reset works from UI and API.
- Reset token cannot be reused.
- Expired tokens are rejected with clear error states.

## Phase 3 — Wallet and funding flows (Week 2)

### Tasks

1. Define wallet provider abstraction (start with one provider + mock adapter).
2. Implement wallet linking flow and state management.
3. Implement funding/deposit/withdraw workflows.
4. Add balance synchronization and reconciliation checks.
5. Replace any “coming soon” wallet UX placeholders with functional screens.

### Acceptance criteria

- User can link a wallet and perform funding operations.
- Balance changes are reflected consistently in UI and DB.
- Failed provider calls produce deterministic rollback/error behavior.

## Phase 4 — Transactions and reliability (Week 2–3)

### Tasks

1. Add idempotency keys for transaction creation and link redemption.
2. Harden transactional boundaries and race-condition handling.
3. Add transaction states (PENDING/SUCCEEDED/FAILED/REVERSED) with explicit transitions.
4. Add retry strategy for transient failures and dead-letter handling for non-recoverable errors.
5. Record audit events for security-sensitive actions.

### Acceptance criteria

- Duplicate client submissions cannot double-spend.
- Concurrent redemption attempts settle to a single winner.
- Every transaction has a full state history and audit trail.

## Phase 5 — Data model and migrations (Week 3)

### Tasks

1. Standardize DB strategy (Postgres for all non-test environments).
2. Introduce migration tooling (versioned migration files).
3. Add seed scripts for local/dev/test.
4. Add DB indices and constraints for hot paths and integrity.

### Acceptance criteria

- Fresh environment can be bootstrapped via migrations only.
- Rollback path is documented and tested.
- Query plans are acceptable for known high-volume endpoints.

## Phase 6 — Testing, CI/CD, and operations (Week 3–4)

### Tasks

1. Add backend unit + integration tests for critical flows.
2. Add frontend e2e smoke tests for auth/pay/recovery journeys.
3. Add CI pipeline gates (lint, test, dependency audit).
4. Add structured logs, health checks, metrics, and alerting thresholds.
5. Add deployment docs/runbooks and backup/restore procedures.

### Acceptance criteria

- CI blocks merges on failed quality gates.
- Core user journeys pass automated e2e tests.
- On-call runbook exists for authentication and transaction incidents.

## MVP Definition of Done

The app is “complete enough” when all items below are true:

- ✅ Secure auth + recovery are fully functional.
- ✅ Wallet linking + funding flow works for at least one provider.
- ✅ Payment links and spend flow are idempotent and auditable.
- ✅ Data migrations, tests, and CI/CD are in place.
- ✅ Production environment has observability and incident runbooks.

## Recommended first implementation slice (next 3–5 days)

1. Security baseline (env hardening, validation, headers, limits).
2. Password recovery backend + UI wiring.
3. Integration tests for register/login/recover/link/redeem happy paths.

This gives the largest risk reduction and unlocks safe iteration on wallet and transaction features.
