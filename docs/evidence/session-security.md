# Session security evidence

Fictional fixture passwords are stored as PBKDF2-SHA256 hashes with per-user salts. Successful sessions receive random bearer tokens and expire after 900 seconds based on a monotonic clock. Expired tokens are removed and return the same generic 401 response as invalid tokens.

Verification:

```bash
./.venv/bin/python -m pytest -q tests/security/test_session_security.py
```

Production authentication would still require a managed identity provider, MFA, secure cookie or token policy, rotation, and operational revocation.
