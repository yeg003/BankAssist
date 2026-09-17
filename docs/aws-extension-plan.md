# AWS extension plan

This is a design only. No AWS resources, credentials, or commands are used in phase one.

## Proposed boundaries

`Browser -> WAF -> API Gateway -> Lambda or private ECS service -> policy layer -> DynamoDB`.
Authentication is issued by Cognito. The model adapter would call Bedrock only through a narrow IAM policy. Secrets Manager stores provider configuration, KMS protects data and logs, and CloudWatch receives structured security events.

## Resource and control plan

| Resource | Purpose | Security boundary |
|---|---|---|
| WAF | Rate rules and managed protections | Only allow API Gateway origin |
| API Gateway | TLS API edge and throttling | JWT authorizer, request schemas |
| Cognito | Customer and reviewer identities | MFA, groups, short-lived tokens |
| Lambda or ECS | Application runtime | Separate task role, no public datastore access |
| DynamoDB | Accounts, transactions, review records | KMS encryption, condition expressions, point-in-time recovery |
| Secrets Manager | Model/provider secrets | Runtime read only, rotation |
| KMS | Encryption keys | Key policy separates administration and use |
| CloudWatch | Logs, metrics, alarms | No prompts, tokens, balances, or raw PII |
| Bedrock | Optional model inference | No authorization authority, private network path where supported |

## Terraform outline

```text
infra/
  main.tf              # provider, backend, module wiring
  variables.tf         # region, environment, retention, limits
  iam.tf               # runtime, deploy, and audit roles
  cognito.tf           # user pool, groups, app client
  api.tf               # API Gateway, JWT authorizer, throttles
  compute.tf           # Lambda or ECS service
  data.tf              # DynamoDB tables and KMS encryption
  observability.tf     # log groups, alarms, metric filters
  waf.tf               # Web ACL and association
```

Terraform plan must be reviewed before apply. The initial plan must contain no permissive `*` data access, no public DynamoDB endpoint, no plaintext secret variables, and no production data fixtures.

## Identity and network model

Customers receive customer-group claims. Reviewers receive reviewer-group claims. The application maps claims to policy decisions and still checks object ownership. Compute runs in private subnets if ECS is selected. Datastore security groups or endpoint policies accept traffic only from the application boundary.

## Logging and monitoring

Emit event type, outcome, actor pseudonym, request ID, and latency. Add alarms for repeated authorization denials, review-approval spikes, model failures, throttling, and unexpected tool proposals. Apply retention and restricted analyst access. Never log prompts, access tokens, balances, full card numbers, or model chain-of-thought.

## Cost and operational assumptions

API Gateway, Lambda, Cognito, DynamoDB on-demand, CloudWatch, WAF, KMS, Secrets Manager, and Bedrock inference are usage-priced. A cost estimate must be produced from expected requests, tokens, log volume, retention, and WAF rules before deployment. The local project does not estimate a live bill and makes no cost guarantee.

## Verification, rollback, and teardown

1. Run `terraform fmt` and `terraform validate`.
2. Run `terraform plan` and review IAM, network, encryption, logging, and public exposure.
3. Deploy only after explicit user approval.
4. Verify health, authentication, object authorization, tool restrictions, review workflow, logging redaction, WAF throttling, and alarms with synthetic data.
5. For rollback, restore the previous immutable application artifact and Terraform state after review.
6. For teardown, disable traffic, export only approved evidence, destroy the stack, remove retained logs and keys according to policy, and verify no resources remain.
