# BankAssist

## Secure AI Banking Support Chatbot

BankAssist is an independent secure AI banking-support chatbot case study. It uses fictional users, mock accounts, and a local test environment to show how an AI-enabled application can be tested, broken, and hardened.

This repository is a portfolio presentation. It contains the explanation and sanitized screenshots, not application source code, credentials, real banking data, or cloud deployment configuration.

## The problem

An AI assistant can produce a convincing answer and still suggest the wrong account, action, or policy. In a banking-support workflow, the model cannot be the authority for identity, ownership, or financial actions.

The project asks a practical question: what happens when the model gets the request wrong?

## What BankAssist demonstrates

- Fictional-user authentication and session expiry
- Account, card, and transaction ownership checks
- Prompt-injection and indirect-injection testing
- Tool allowlisting and least-privilege actions
- Structured response validation
- PII detection and redaction
- Human review for high-risk requests
- Privacy-minimized audit logging
- Rate limiting and secure error handling
- OWASP Top 10 for LLM Applications and MITRE ATLAS mapping

## The application

The local interface supports demo sign-in, account questions, transaction explanations, fictional card status, fraud guidance, clean and poisoned support tickets, response inspection, and high-risk review routing.

![BankAssist login screen](screenshots/login.png)

![BankAssist conversation screen](screenshots/conversation.png)

The full local workflow capture includes account lookup, transaction explanation, fraud guidance, ticket handling, and high-risk review routing.

![Full BankAssist workflow](screenshots/full-workflow.png)

## How the security boundary works

The model proposes a structured action. Deterministic backend code then verifies the signed-in user, checks resource ownership, enforces the tool allowlist, validates the response, and decides whether the request can proceed.

The model generates suggestions. The backend decides what is allowed.

## Before and after

The following captures show freshly executed local API tests. Both authenticate as Alex and request Blair's fictional account. These are browser-rendered test-output reports, separate from the customer chat interface. No LLM is called in this comparison.

### Before: ownership check disabled

The isolated baseline returned HTTP 200 for the unauthorized request. The test verified that the response identified Blair's account. Alex's own account also returned HTTP 200 as a control.

![Baseline API test output showing unauthorized access with HTTP 200](screenshots/before-cross-account-200.png)

### After: ownership check enforced

The same test returned HTTP 403 with `Access denied`. Alex's own account still returned HTTP 200. Both tests passed their expected assertions; the baseline PASS means the intentional vulnerability was reproduced.

![Hardened API test output showing access denied with HTTP 403](screenshots/after-cross-account-403.png)

The separate scripted prompt-injection exercise simulates an unsafe model proposal. It does not establish that Qwen was compromised. The existing chat screenshot below records a model refusal and explicitly states that backend authorization was not exercised.

![Chat model refusal with backend authorization not exercised](screenshots/hardened-cross-account-blocked.png)

## Security testing

Authorized local tests covered:

- Cross-account account and transaction access
- Prompt injection and identity override attempts
- System-instruction disclosure attempts
- Poisoned support-ticket content
- Unsafe tool proposals and transfer attempts
- Malformed input and unsafe model output
- Human-approval bypass attempts
- Sensitive data appearing in logs
- Rate-limit behavior

The evidence records the attack scenario, affected asset, trust boundary, impact, reproduction steps, expected result, observed result, risk rating, and remediation. Results are scoped to this fictional local application and are not a general security score.

## Framework coverage

The case study maps implemented controls and test evidence to the OWASP Top 10 for LLM Applications and relevant MITRE ATLAS techniques. Anything not directly tested is marked as not verified in the project evidence.

## Scope and limitations

BankAssist does not connect to production banking systems, move money, make financial decisions, or use real customer information. AWS deployment is a future design only. Local audit events and sessions are intentionally limited to the demo environment.

## Portfolio note

This repository contains the project narrative and sanitized screenshots so the security reasoning is easy to review. The implementation remains local and private to the development workspace.
