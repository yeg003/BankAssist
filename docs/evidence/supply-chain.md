# Supply-chain evidence

Direct runtime and development dependencies are recorded in `requirements.lock`. The verifier compares those versions with the active environment and prints SHA256 fingerprints for application Python files.

```bash
./.venv/bin/python scripts/verify_supply_chain.py
./.venv/bin/python -m pytest -q tests/security/test_supply_chain.py
```

This is scoped local reproducibility evidence, not a full software-supply-chain attestation. Transitive package provenance, vulnerability scanning, signatures, and future model artifact pinning remain separate work.

Offline consistency check:

```bash
./.venv/bin/python scripts/dependency_check.py
```

The legacy Python 3.9 environment returned no broken requirements but has seven known advisories. The supported Python 3.11 environment was audited directly with:

```bash
.venv-py311/bin/pip-audit --path .venv-py311/lib/python3.11/site-packages
```

It returned `No known vulnerabilities found`; the local editable package was skipped because it is not published to PyPI. Requirements-file auditing remains limited by the local temporary-venv `ensurepip` crash.

A fully hashed lock was generated for Python 3.11 with `pip-compile --generate-hashes`. It can be audited without temporary dependency resolution:

```bash
.venv-py311/bin/pip-audit --disable-pip --no-deps -r requirements-py311-hashed.lock
```

Result: `No known vulnerabilities found`.
