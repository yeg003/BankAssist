# Claims and evidence

| Claim | Evidence | Scope |
|---|---|---|
| Cross-account access is blocked | Account/card/transaction replay tests | Fictional local fixtures |
| Model output is not authorization | Tool policy and browser inspection tests | Local Qwen proposal path |
| Prompt-injected ticket content is isolated | Lesson 3 poisoned-ticket run | Two supplied fictional tickets |
| High-risk requests require review | Review queue tests and browser walkthrough | No real action executes |
| Browser flow works | 25-check Playwright suite plus reviewer QA | Local Chromium run |
| Supply-chain versions are reproducible | Lockfile and verifier | Direct dependencies only |
| No vulnerabilities exist | NOT CLAIMED | `pip-audit` found 7 open advisories |
| Dependency scan is clean | NOT CLAIMED | Current pip-audit run found 7 open advisories; remediation is planned |
