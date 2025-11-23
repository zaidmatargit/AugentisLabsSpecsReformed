## API endpoint standards and conventions

### General REST Principles
- **RESTful Design**: Follow REST principles with clear resource-based URLs and appropriate HTTP methods (GET, POST, PUT, PATCH, DELETE)
- **Consistent Naming**: Use kebab-case for URL paths (e.g., `/quality-gates`, `/user-stories`)
- **Versioning**: Use URL path versioning (`/api/v1/ventures`) to manage breaking changes
- **Plural Nouns**: Use plural nouns for resource endpoints (e.g., `/ventures`, `/agents`, `/artifacts`)
- **Nested Resources**: Limit nesting depth to 2-3 levels maximum (e.g., `/ventures/:id/phases/:phase/agents`)
- **Query Parameters**: Use query parameters for filtering, sorting, pagination, search
- **HTTP Status Codes**: Return appropriate status codes (200, 201, 400, 401, 403, 404, 409, 500)
- **Rate Limiting Headers**: Include `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset` headers

### AugentisLabs 5D Phase API Conventions

#### Base URL Structure
All APIs use `/api/v1` prefix:
```
https://api.augentislabs.com/api/v1/{resource}
```

#### Venture Resource Endpoints
```
GET    /api/v1/ventures                    # List all ventures (tenant-scoped)
POST   /api/v1/ventures                    # Create new venture
GET    /api/v1/ventures/:id                # Get venture by ID
PATCH  /api/v1/ventures/:id                # Update venture
DELETE /api/v1/ventures/:id                # Delete venture (soft delete)
GET    /api/v1/ventures/:id/status         # Get venture status with phase progression
```

#### Discover Phase Endpoints
```
POST   /api/v1/ventures/:id/discover/start             # Start Discover phase
GET    /api/v1/ventures/:id/discover/agents            # List Discover phase agents
POST   /api/v1/ventures/:id/discover/agents/:agentId   # Execute specific agent
GET    /api/v1/ventures/:id/discover/artifacts         # Get Discover artifacts
GET    /api/v1/ventures/:id/discover/vrc-gate          # Calculate VRC gate score
POST   /api/v1/ventures/:id/discover/complete          # Complete Discover phase (validates VRC ≥76%)
```

#### Define Phase Endpoints
```
POST   /api/v1/ventures/:id/define/start               # Start Define phase
POST   /api/v1/ventures/:id/define/personas            # Generate personas
POST   /api/v1/ventures/:id/define/prd                 # Generate PRD
GET    /api/v1/ventures/:id/define/user-stories        # List user stories
GET    /api/v1/ventures/:id/define/vcd-gate            # Calculate VCD gate score
POST   /api/v1/ventures/:id/define/complete            # Complete Define phase (validates VCD ≥75%)
```

#### Design Phase Endpoints
```
POST   /api/v1/ventures/:id/design/start               # Start Design phase
POST   /api/v1/ventures/:id/design/wireframes          # Generate wireframes
POST   /api/v1/ventures/:id/design/architecture        # Generate system architecture
POST   /api/v1/ventures/:id/design/data-model          # Generate Prisma schema
POST   /api/v1/ventures/:id/design/api-contracts       # Generate OpenAPI specs
GET    /api/v1/ventures/:id/design/dsp-gate            # Calculate DSP gate score
POST   /api/v1/ventures/:id/design/complete            # Complete Design phase (validates DSP ≥80%)
```

#### Develop Phase Endpoints
```
POST   /api/v1/ventures/:id/develop/start              # Start Develop phase
POST   /api/v1/ventures/:id/develop/generate-tests     # Generate failing tests (RED)
POST   /api/v1/ventures/:id/develop/generate-code      # Generate code to pass tests (GREEN)
GET    /api/v1/ventures/:id/develop/test-results       # Get test execution results
GET    /api/v1/ventures/:id/develop/coverage           # Get test coverage metrics
GET    /api/v1/ventures/:id/develop/mdp-gate           # Calculate MDP gate score
POST   /api/v1/ventures/:id/develop/complete           # Complete Develop phase (validates MDP ≥85%)
```

#### Deploy Phase Endpoints
```
POST   /api/v1/ventures/:id/deploy/start               # Start Deploy phase
POST   /api/v1/ventures/:id/deploy/provision           # Provision infrastructure
POST   /api/v1/ventures/:id/deploy/release             # Deploy to production
GET    /api/v1/ventures/:id/deploy/telemetry           # Get production telemetry
POST   /api/v1/ventures/:id/deploy/divergence-check    # Check for spec divergence
GET    /api/v1/ventures/:id/deploy/health              # Get deployment health metrics
```

#### Quality Gate Endpoints
```
GET    /api/v1/ventures/:id/gates                      # List all gate scores
GET    /api/v1/ventures/:id/gates/:gateType            # Get specific gate (VRC|VCD|DSP|MDP)
POST   /api/v1/ventures/:id/gates/:gateType/calculate  # Recalculate gate score
POST   /api/v1/ventures/:id/gates/:gateType/override   # Override gate (requires approval)
```

#### Artifact Endpoints
```
GET    /api/v1/ventures/:id/artifacts                  # List all artifacts
GET    /api/v1/ventures/:id/artifacts/:artifactId      # Get artifact by ID
GET    /api/v1/ventures/:id/artifacts/:type            # Get artifacts by type (prd, wireframes, schema, etc.)
GET    /api/v1/ventures/:id/artifacts/:id/versions     # Get artifact version history
POST   /api/v1/ventures/:id/artifacts/:id/export       # Export artifact to S3
```

#### Agent Execution Endpoints
```
GET    /api/v1/agents                                  # List all available agents
GET    /api/v1/agents/:agentId                         # Get agent details
POST   /api/v1/agents/:agentId/execute                 # Execute agent (async)
GET    /api/v1/agents/executions/:executionId          # Get execution status
POST   /api/v1/agents/executions/:executionId/cancel   # Cancel running execution
```

#### Telemetry & Governance Endpoints
```
GET    /api/v1/governance/audit-logs                   # Get audit logs (admin only)
GET    /api/v1/governance/cost-tracking                # Get cost tracking by tenant
GET    /api/v1/telemetry/divergence-alerts             # Get divergence alerts
GET    /api/v1/telemetry/metrics                       # Get system metrics
```

### Multi-Tenant Isolation
All endpoints automatically scope to tenant context from JWT:

**Request Headers**:
```
Authorization: Bearer <jwt-token>
X-Tenant-ID: <tenant-uuid>
```

**Tenant Context Extraction**:
- Extract `tenantId` from JWT claims
- Validate `X-Tenant-ID` header matches JWT tenant
- Apply Prisma RLS filter: `where: { tenantId: user.tenantId }`
- Return 403 Forbidden on cross-tenant access attempt

### Response Format Standards

#### Success Response (200 OK)
```json
{
  "data": { ... },
  "meta": {
    "timestamp": "2025-11-22T13:46:00.000Z",
    "correlationId": "uuid"
  }
}
```

#### Created Response (201 Created)
```json
{
  "data": { "id": "uuid", ... },
  "meta": {
    "timestamp": "2025-11-22T13:46:00.000Z",
    "correlationId": "uuid"
  }
}
```

#### Error Response (4xx/5xx)
```json
{
  "statusCode": 400,
  "message": "User-friendly error message",
  "error": "BadRequest",
  "timestamp": "2025-11-22T13:46:00.000Z",
  "path": "/api/v1/ventures/123",
  "correlationId": "uuid",
  "details": { ... }  // Optional field-level errors
}
```

#### Paginated Response
```json
{
  "data": [...],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8,
    "hasNext": true,
    "hasPrev": false
  }
}
```

### Query Parameter Conventions

#### Pagination
```
GET /api/v1/ventures?page=1&limit=20
```

#### Filtering
```
GET /api/v1/ventures?phase=DISCOVER&status=active
```

#### Sorting
```
GET /api/v1/ventures?sortBy=createdAt&order=desc
```

#### Search
```
GET /api/v1/ventures?search=ecommerce
```

#### Field Selection (Sparse Fieldsets)
```
GET /api/v1/ventures?fields=id,name,phase
```

### HTTP Method Conventions
- **GET**: Retrieve resources (idempotent, cacheable)
- **POST**: Create resources or trigger actions (non-idempotent)
- **PATCH**: Partial update (send only changed fields)
- **PUT**: Full replacement (send entire resource)
- **DELETE**: Soft delete (set `deletedAt` timestamp)

### Status Code Usage
- **200 OK**: Successful GET, PATCH, DELETE
- **201 Created**: Successful POST (resource created)
- **204 No Content**: Successful DELETE (no response body)
- **400 Bad Request**: Validation errors, malformed request
- **401 Unauthorized**: Missing or invalid authentication token
- **403 Forbidden**: Valid auth, insufficient permissions or tenant isolation violation
- **404 Not Found**: Resource not found
- **409 Conflict**: Quality gate failure, resource state conflict
- **429 Too Many Requests**: Rate limit exceeded
- **500 Internal Server Error**: Unexpected server errors
- **503 Service Unavailable**: External service unavailable, circuit breaker open

### Performance Requirements
- **P95 Latency**: <300ms for all endpoints
- **Timeout**: 30 seconds for synchronous endpoints
- **Async Operations**: Use background jobs for long-running tasks (>5 seconds)
  - Return 202 Accepted immediately
  - Provide `/status/:jobId` endpoint to check progress
  - Use webhooks or polling for completion notification

### Rate Limiting
Per tenant limits (enforced via Redis):
- **Standard Tier**: 1000 requests/hour
- **Professional Tier**: 5000 requests/hour
- **Enterprise Tier**: Custom limits

**Response Headers**:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 847
X-RateLimit-Reset: 1700654400
```

### Authentication & Authorization
- **JWT Tokens**: Use Supabase Auth for token generation
- **Token Expiry**: 1 hour (refresh tokens valid for 30 days)
- **Multi-Tenant Isolation**: Every request validates tenant context
- **Role-Based Access**: Admin, Member, Viewer roles per tenant

### OpenAPI 3.0 Compliance
- All APIs must be documented in OpenAPI 3.0 specs
- Contract tests validate request/response compliance
- Breaking changes require major version bump (`/api/v2`)
- OpenAPI specs stored in: `specs/master/contracts/api-{phase}.yaml`

### CORS Configuration
```typescript
{
  origin: ['https://app.augentislabs.com', 'http://localhost:3000'],
  credentials: true,
  methods: ['GET', 'POST', 'PATCH', 'DELETE'],
  allowedHeaders: ['Authorization', 'Content-Type', 'X-Tenant-ID', 'X-Correlation-ID']
}
```

### Idempotency
- Use `X-Idempotency-Key` header for POST/PATCH requests
- Store idempotency keys in Redis with 24-hour TTL
- Return cached response if duplicate request detected

### API Deprecation Policy
1. Announce deprecation 90 days in advance
2. Add `Deprecation` and `Sunset` headers to deprecated endpoints
3. Provide migration guide in API documentation
4. Maintain deprecated endpoints for minimum 180 days
5. Return 410 Gone after sunset date
