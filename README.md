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

## How the security boundary works

The model proposes a structured action. Deterministic backend code then verifies the signed-in user, checks resource ownership, enforces the tool allowlist, validates the response, and decides whether the request can proceed.

The model generates suggestions. The backend decides what is allowed.

## Before and after

The baseline version intentionally trusted the model-requested account target. A prompt-injection test changed the request from Alex’s account to Blair’s fictional account, and the backend returned HTTP 200 with account data.

![Prompt injection and conversation evidence](screenshots/conversation.png)

After hardening, the same cross-account request returned HTTP 403 and no account data. The fix was server-side ownership enforcement, not a better prompt.

![Hardened cross-account refusal](screenshots/hardened-cross-account-blocked.png)

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
