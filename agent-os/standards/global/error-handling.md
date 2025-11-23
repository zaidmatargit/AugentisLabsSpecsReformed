## Error handling best practices

### General Principles
- **User-Friendly Messages**: Provide clear, actionable error messages to users without exposing technical details or security information
- **Fail Fast and Explicitly**: Validate input and check preconditions early; fail with clear error messages rather than allowing invalid state
- **Specific Exception Types**: Use specific exception/error types rather than generic ones to enable targeted handling
- **Centralized Error Handling**: Use NestJS exception filters for API error handling; Next.js error boundaries for frontend
- **Graceful Degradation**: Design systems to degrade gracefully when non-critical services fail rather than breaking entirely
- **Clean Up Resources**: Always clean up resources (database connections, file handles) in finally blocks or using `try-finally`

### AugentisLabs-Specific Error Handling

#### LangGraph Agent Error Recovery
- **State Persistence**: Save agent state to database before each LLM call to enable recovery from failures
- **Retry with Exponential Backoff**:
  - Initial retry: 1 second
  - Max retries: 3 attempts
  - Backoff multiplier: 2x (1s, 2s, 4s)
  - Jitter: Add random ±20% to prevent thundering herd
- **Fallback LLM Provider**: If OpenAI fails after retries, fall back to Anthropic (and vice versa)
- **Circuit Breaker Pattern**: After 5 consecutive failures, open circuit for 60 seconds before retrying
- **Recovery Checkpoints**: Save checkpoints after each phase gate completion (VRC, VCD, DSP, MDP) to enable resumption

#### Multi-Tenant Isolation Errors
- **Tenant Context Missing**: Return 401 Unauthorized if tenant context is missing from request
- **Cross-Tenant Access Attempt**: Return 403 Forbidden if user attempts to access another tenant's resources
- **RLS Policy Violations**: Log RLS violations as security events; return 403 to user
- **Guard Failures**: Use NestJS guards to validate tenant isolation; throw `ForbiddenException` on violations

#### API Error Response Format
Use consistent error response structure across all endpoints:

```typescript
{
  "statusCode": 400,
  "message": "User-friendly error message",
  "error": "BadRequest",
  "timestamp": "2025-11-22T13:46:00.000Z",
  "path": "/api/ventures/123",
  "correlationId": "uuid-for-tracing"
}
```

#### NestJS Exception Hierarchy
- **400 Bad Request**: Invalid input, validation failures (use `BadRequestException`)
- **401 Unauthorized**: Missing or invalid authentication token (use `UnauthorizedException`)
- **403 Forbidden**: Valid auth, but insufficient permissions or tenant isolation violation (use `ForbiddenException`)
- **404 Not Found**: Resource not found (use `NotFoundException`)
- **409 Conflict**: Resource state conflict, quality gate failure (use `ConflictException`)
- **429 Too Many Requests**: Rate limit exceeded (use custom `RateLimitException`)
- **500 Internal Server Error**: Unexpected errors, LLM failures (use `InternalServerErrorException`)
- **503 Service Unavailable**: External service unavailable, circuit breaker open (use `ServiceUnavailableException`)

#### Quality Gate Failures
- **Gate Threshold Not Met**: Return 409 Conflict with detailed gate metrics
- **Missing Evidence**: Return 400 Bad Request with list of missing evidence items
- **Phase Bypass Attempt**: Return 403 Forbidden; log security event; require executive approval override
- **Response Format**:
  ```typescript
  {
    "statusCode": 409,
    "message": "VRC quality gate threshold not met (score: 72%, required: 76%)",
    "error": "QualityGateFailure",
    "gateType": "VRC",
    "currentScore": 0.72,
    "requiredScore": 0.76,
    "missingCriteria": ["market_validation", "competitor_analysis"]
  }
  ```

#### LLM-Specific Error Handling
- **Rate Limit Errors**: Catch 429 errors; implement exponential backoff; track usage to prevent
- **Token Limit Exceeded**: Catch 400 context_length_exceeded; truncate input; retry with shorter prompt
- **Invalid API Key**: Log as critical error; send alert to ops team; return 503 to user
- **Timeout Errors**: Set 30-second timeout for LLM calls; return partial results if available
- **Content Filter Violations**: Log the prompt that triggered filter; return sanitized error to user

#### Database Error Handling (Prisma)
- **Unique Constraint Violation** (`P2002`): Return 409 Conflict with field name
- **Foreign Key Violation** (`P2003`): Return 400 Bad Request with relationship details
- **Record Not Found** (`P2025`): Return 404 Not Found
- **Connection Timeout** (`P1008`): Retry with exponential backoff (3 attempts)
- **Transaction Deadlock** (`P2034`): Retry transaction up to 3 times
- **RLS Violation**: Log as security event; return 403 Forbidden

#### Frontend Error Handling (Next.js)
- **Error Boundaries**: Wrap each route with error boundary to catch React errors
- **API Call Failures**: Display user-friendly toast notifications for failed API calls
- **Network Errors**: Detect offline state; queue mutations for retry when online
- **Validation Errors**: Display field-level validation errors inline on forms
- **Suspense Fallbacks**: Show loading skeletons during async operations

#### Logging and Monitoring
- **Error Logging**: Log all errors to Sentry with full context (user, tenant, request, stack trace)
- **Correlation IDs**: Include correlation ID in all logs and error responses for tracing
- **Error Metrics**: Track error rates by endpoint, tenant, and error type
- **Alerting Thresholds**:
  - Critical: Error rate >5% for any endpoint
  - Warning: Agent execution success rate <95%
  - Warning: API P95 latency >300ms
- **PII Redaction**: Redact sensitive data (emails, tokens) from error logs before sending to Sentry

#### Telemetry and Divergence Detection
- **Divergence Alerts**: When production telemetry detects divergence from spec, log as warning (not error)
- **Selective Re-execution**: Divergence triggers cascade impact analysis; re-execute affected components only
- **Evidence Tier Updates**: Update evidence tier to E4 (production-proven) after successful deployment
- **Rollback Triggers**: Automatic rollback if error rate exceeds 10% within first hour of deployment
