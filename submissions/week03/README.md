# Week 3 Individual Readiness Lab

This submission documents a small, bounded engineering change to the MAIE 6000C starter repository.

The change extends the existing keyword-based AI triage behavior while preserving the current service architecture and workflow.

## Purpose of this change

The AI service classifies support cases using predefined keyword groups.

Before this change, a case containing the word `renewal` without any of the existing billing keywords was classified as `general`.

The purpose of this change is to recognize `renewal` as a billing-related keyword so that these cases are classified more appropriately.

## Bounded change

The following keyword was added to the existing `billing` category:

```text
renewal
```

The billing keyword set now includes:

```text
invoice
charge
refund
billing
payment
subscription
renewal
```

No API routes, database models, worker behavior, or service interfaces were changed.

## Files changed

### AI service

`services/ai/app/main.py`

Added `renewal` to the existing billing keyword set used by `triage_text()`.

### Unit tests

`tests/unit/test_ai_service.py`

Added a new unit test:

```text
test_triage_billing_renewal_case
```

The test verifies that a case containing `renewal` is classified as `billing`.

## Verification

### Baseline verification

Before making the change, the running services were checked using:

```bash
curl http://localhost:8000/health/live
curl http://localhost:8000/health/ready
curl http://localhost:8100/health/live
```

The API liveness check, API readiness check, and AI service liveness check all returned status `ok`.

The original full test suite was also run:

```bash
docker compose run --rm --no-deps api pytest -q
```

Baseline result:

```text
4 passed, 1 skipped
```

### Test before implementation

The new unit test was added before changing the billing keyword set.

The test initially failed because the AI service returned:

```text
general
```

instead of the expected:

```text
billing
```

The failure confirmed that the new behavior was not already supported.

### Test after implementation

After adding `renewal` to the billing keyword set, the unit tests were run again:

```bash
docker compose run --rm --no-deps api pytest -q tests/unit/test_ai_service.py
```

Result:

```text
3 passed
```

The complete test suite was then run:

```bash
docker compose run --rm --no-deps api pytest -q
```

Final result:

```text
5 passed, 1 skipped, 2 warnings
```

The warnings were dependency deprecation warnings and did not cause any test failures.

## Result

The AI triage service now classifies cases containing the keyword `renewal` as `billing`.

The change is intentionally small and limited to the existing keyword-based triage behavior. Existing tests continue to pass, and the repository remains operational after the change.

## AI Use Statement

ChatGPT was used to help interpret the assignment requirements, understand the Git and Docker workflow, and plan the test-first verification process.

I manually edited the repository files, ran the Docker and Git commands locally, reviewed the code changes, and verified the behavior using the project test suite. I also reviewed the generated suggestions before applying them to the repository.
