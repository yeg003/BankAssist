# Phase 01 Evidence: Authentication and Object Authorization

Date: 2026-09-15

## Automated verification

Command:

```bash
.venv/bin/python -m pytest
```

Observed output:

```text
.......                                                                  [100%]
7 passed in 0.03s
```

Result: PASS. This proves the current unit and integration suite exercised health status, fictional login, invalid-login logging, owner access, missing-token rejection, hardened cross-account denial, and the explicit baseline-only BOLA demonstration.

## Local release smoke check

Command:

```bash
curl --fail --silent --show-error http://127.0.0.1:8008/health
```

Observed output:

```json
{"status":"ok","security_mode":"hardened"}
```

Result: PASS. This proves the FastAPI application was running locally in hardened mode.

## Hardened authorization smoke check

Command:

```bash
.venv/bin/python - <<'PY'
import httpx
with httpx.Client(base_url='http://127.0.0.1:8008') as client:
    login = client.post('/auth/login', json={'username': 'alex', 'password': 'demo-alex-password'})
    token = login.json()['access_token']
    denied = client.get('/accounts/acct_demo_blair_savings', headers={'Authorization': f'Bearer {token}'})
    print(f'login_status={login.status_code}')
    print(f'cross_account_status={denied.status_code}')
    print(f'cross_account_body={denied.json()}')
PY
```

Observed output:

```text
login_status=200
cross_account_status=403
cross_account_body={'detail': 'Access denied'}
```

Result: PASS. This proves an authenticated fictional user cannot retrieve another fictional user's account in hardened mode.

## Static and secret-pattern checks

Commands:

```bash
.venv/bin/python -m compileall -q app tests
rg -n --hidden -g '!*.pyc' -g '!.venv/**' '(AKIA[0-9A-Z]{16}|BEGIN (RSA |OPENSSH )?PRIVATE KEY|aws_secret_access_key|sk-[A-Za-z0-9]{20,})' .
```

Observed output: `compileall` produced no errors. The secret-pattern scan produced no matches.

Result: PASS for this narrow scan only. It does not prove absence of every possible secret format.

## Security relevance

- OWASP LLM06 assessment target: deterministic authorization constrains an eventual AI-driven tool request.
- MITRE ATLAS assessment target: a local cross-account data-access abuse scenario is covered by paired baseline and hardened tests.
- Audit tests confirm denied access does not write the requested account identifier or password into the local event record.

## Not verified yet

- Browser UI and Playwright coverage
- Chat, prompt-injection, output-validation, PII-redaction, escalation, rate-limit, and tool-allowlist controls
- AWS extension, intentionally deferred
