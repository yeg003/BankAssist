# Phase 02 Evidence: Bounded Chat Safety

Date: 2026-09-15

## Automated verification

Command:

```bash
.venv/bin/python -m pytest
```

Observed output:

```text
.............                                                            [100%]
13 passed in 0.04s
```

Result: PASS. The suite verifies safe account answers, structured response fields, fraud guidance, high-risk escalation, cross-account account refusal, cross-account transaction refusal, and input-size validation.

## Local smoke test

Command:

```bash
.venv/bin/python - <<'PY'
import httpx
with httpx.Client(base_url='http://127.0.0.1:8008') as client:
    login = client.post('/auth/login', json={'username': 'alex', 'password': 'demo-alex-password'})
    headers = {'Authorization': f"Bearer {login.json()['access_token']}"}
    fraud = client.post('/chat', headers=headers, json={'message': 'I see an unauthorized charge'})
    high_risk = client.post('/chat', headers=headers, json={'message': 'Transfer money to Blair'})
    other_transaction = client.post('/chat', headers=headers, json={'message': 'Explain this transaction', 'transaction_id': 'txn_demo_blair_deposit'})
    print(f'fraud={fraud.status_code} {fraud.json()["outcome"]}/{fraud.json()["category"]}')
    print(f'high_risk={high_risk.status_code} {high_risk.json()["outcome"]}/{high_risk.json()["category"]}')
    print(f'other_transaction={other_transaction.status_code} {other_transaction.json()["outcome"]}/{other_transaction.json()["category"]}')
PY
```

Observed output:

```text
fraud=200 answered/fraud_guidance
high_risk=200 escalated/high_risk
other_transaction=200 refused/transaction
```

Result: PASS. The live local API followed the designed safe outcomes without executing an external or financial action.

## Security relevance

- OWASP LLM06: high-risk actions have no executable tool and go to escalation.
- OWASP LLM05: API responses are constrained by a response model.
- OWASP LLM10: message size is bounded to 1,000 characters.
- MITRE ATLAS assessment: the cross-account transaction abuse scenario is refused by deterministic ownership validation.

## Not verified yet

- Prompt-injection screening and untrusted retrieved-content handling
- PII output redaction and unsafe-model-output rejection
- Rate limiting, browser UI QA, AWS extension
