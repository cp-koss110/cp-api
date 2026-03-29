# Copilot Code Review Instructions — cp-api

## What this service does
REST microservice that receives email payloads over HTTP, validates a bearer token against AWS SSM Parameter Store, and publishes the message body to SQS.

## Stack
- Python 3.12, FastAPI, Pydantic v2, boto3
- Deployed on ECS Fargate behind an ALB
- LocalStack for local development

## Review priorities
- **Security**: token validation must always happen before any SQS publish. The token is fetched from SSM (`/exam-costa/{env}/api/token`) with decryption — never hardcoded.
- **Payload validation**: the `data` field must contain all four text fields (`email_subject`, `email_sender`, `email_timestream`, `email_content`). Reject anything else with 422.
- **AWS calls**: all boto3 calls should handle `ClientError` and `BotoCoreError`. No bare `except Exception` swallowing errors silently.
- **Tests**: unit tests live in `tests/`, integration tests in `tests/integration/`. Integration tests use LocalStack with hardcoded `test`/`test` credentials — never real AWS creds.
- **Dependencies**: `requirements.txt` for runtime, `requirements-dev.txt` for dev/test. Do not mix them.

## What to ignore
- `coverage.xml`, `coverage-summary.md` — generated files
- `.secrets.baseline` — detect-secrets baseline, not sensitive
