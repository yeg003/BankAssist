# Security Framework Mapping

## Purpose and evidence rules

BankAssist will use OWASP Top 10 for LLM Applications 2025 and MITRE ATLAS as threat-informed assessment frameworks. A mapping is not a compliance certification and does not imply that every framework item is applicable to a local, offline mock application.

Each row will receive one of these evidence statuses after tests exist: `PASS`, `FAIL`, `NOT VERIFIED`, or `NOT APPLICABLE WITH RATIONALE`.

## Controlled baseline safety contract

The project will deliberately demonstrate selected weaknesses only in an offline, localhost-only baseline test configuration. The baseline uses fictional fixtures, cannot call external services, contains no write-capable financial tools, and is disabled by default. Its purpose is to show a finding, its impact within this mock system, the remediation, and a passing regression test. The hardened configuration is the only documented local release configuration.

## OWASP Top 10 for LLM Applications 2025

| OWASP risk | BankAssist assessment target | Planned control | Evidence | Status |
|---|---|---|---|
| LLM01 Prompt Injection | Direct and indirect instruction override | Separate trusted instructions, screen untrusted content, never grant authority from model text | Lesson 3/4 and browser attack evidence | PASS scoped |
| LLM02 Sensitive Information Disclosure | Mock PII, system-instruction, log leakage | Data minimization, output redaction, generic refusals, log redaction | privacy, audit, and refusal tests | PASS scoped |
| LLM03 Supply Chain | Dependencies and local fixtures | Pinned direct dependencies, dependency review, fixture provenance | `requirements.lock`, `scripts/verify_supply_chain.py`, provenance fingerprints | PASS scoped |
| LLM04 Data and Model Poisoning | Untrusted retrieved support content | Offline allowlisted fixtures, provenance tags, treat content as data not instructions | Lesson 3 poisoned-ticket evidence | PASS scoped |
| LLM05 Improper Output Handling | Unsafe or malformed mock-model response | Strict response schema, output validation, safe rendering | local adapter and browser HTML tests | PASS scoped |
| LLM06 Excessive Agency | Money movement, ownership changes, over-broad tools | Explicit read-only tool allowlist, least privilege, deterministic authorization, human review | tool misuse and review queue evidence | PASS scoped |
| LLM07 System Prompt Leakage | Requests to reveal instructions | Do not return trusted instructions, validate response content | attack-suite refusal coverage | PASS scoped |
| LLM08 Vector and Embedding Weaknesses | Retrieval design | No vector database in phase one; retrieval extension assessed before addition | No vector store exists in phase one | NOT APPLICABLE WITH RATIONALE |
| LLM09 Misinformation | Incorrect financial guidance | Offline curated responses, limitation language, escalation for action requests | general-response and escalation tests | PASS scoped |
| LLM10 Unbounded Consumption | Excessive requests or response size | Request validation, response limits, local rate limiting | rate-limit and browser evidence | PASS scoped |

## MITRE ATLAS assessment model

MITRE ATLAS will drive threat scenarios rather than a claim of complete technique coverage. We will map each authorized local test to the ATLAS tactic or technique selected from the current official ATLAS knowledge base at the time of the test.

| BankAssist adversary scenario | ATLAS assessment focus | Planned defensive evidence |
|---|---|---|
| Direct prompt injection to override rules | Model-behavior manipulation | Input screening, deterministic authorization, blocked-event log |
| Poisoned mock support content | Indirect prompt injection and trusted-content abuse | Provenance marking, content isolation, output validation |
| Cross-account account or transaction request | AI-enabled application data-access abuse | Session-bound object authorization and denial test |
| Request to transfer funds or change ownership | Excessive AI agent/tool authority | Tool allowlist, human-review record, refusal test |
| PII or trusted-instruction extraction | Collection and exfiltration attempt | Redaction, generic response, log inspection |
| Malformed inputs and high-volume requests | Service disruption or abuse | Schema validation, bounded inputs, rate-limit test |

## Course connection

The project will cite only actual local course evidence: Module 4's offline input/output guardrails and bound-tool pattern, Module 5's mock-safe tool-abuse harness and least-privilege approach, and Module 8's tested Build, Break, Harden loop. BankAssist remains independently designed and implemented.

## Residual-risk rule

An LLM cannot be treated as a security control. Detection is not prevention, and untested controls remain `NOT VERIFIED`.
