# Abuse-protection evidence

The hardened browser assistant limits each client key to 20 requests in a 60-second in-memory window. A blocked request receives a generic HTTP 429 response and `Retry-After: 60`. The limiter runs before model invocation, so rejected traffic cannot consume model capacity.

This is a local demonstration, not production-grade distributed rate limiting.

Verification command:

```bash
./.venv/bin/python -m pytest -q tests/security/test_rate_limit.py tests/security/test_browser_api.py -k rate_limit
```

The tests verify the limit, expiry, key separation, generic 429 behavior, and that the model is not called after blocking. This maps to OWASP LLM10 Unbounded Consumption and the related MITRE ATLAS abuse scenario.
