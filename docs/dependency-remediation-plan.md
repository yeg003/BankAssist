# Dependency remediation plan

## Current finding

The 2026-09-16 `pip-audit -r requirements.lock` run reported seven advisories affecting pytest 8.4.2, Starlette 0.49.3, and Click 8.1.8. These are development/runtime dependencies in the local case study and must not be ignored.

## Planned remediation

1. Move the supported runtime to Python 3.10 or newer in an isolated environment.
2. Select compatible FastAPI and Starlette versions that include the advisory fixes.
3. Upgrade pytest and Click to versions supported by that interpreter.
4. Recreate the lock file from the clean environment.
5. Run `pip check`, `pip-audit`, the full Python suite, and both browser suites.
6. Review the dependency diff and record before/after scan output.

No upgrade is claimed until all tests and the scan pass. No AWS or production environment is involved.

## Python 3.11 result

An isolated `.venv-py311` was created without changing the original `.venv`. After upgrading pip, pytest, and setuptools, `pip-audit` reported `No known vulnerabilities found` for installed third-party packages; the local editable BankAssist package was skipped because it is not published to PyPI. The full test suite passed under Python 3.11. The environment remains separate until the runtime support decision is reviewed.

Decision for this case study: Python 3.11+ is now the supported runtime and primary lockfile. The legacy Python 3.9 environment is retained only to preserve earlier lesson replay and is not presented as vulnerability-free.
