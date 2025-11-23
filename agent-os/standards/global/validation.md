## Validation best practices

### General Principles
- **Validate on Server Side**: Always validate on the server using NestJS pipes; never trust client-side validation alone
- **Client-Side for UX**: Use React Hook Form + Zod for immediate user feedback, but duplicate checks server-side
- **Fail Early**: Validate input as early as possible using NestJS ValidationPipe at controller level
- **Specific Error Messages**: Provide clear, field-specific error messages that help users correct their input
- **Allowlists Over Blocklists**: Define what is allowed rather than trying to block everything that's not allowed
- **Type and Format Validation**: Use class-validator decorators (@IsString, @IsEmail, @IsUUID, etc.)
- **Sanitize Input**: Sanitize user input to prevent injection attacks (SQL, XSS, command injection)
- **Business Rule Validation**: Validate business rules in service layer, not controllers
- **Consistent Validation**: Apply validation consistently across all entry points (API endpoints, background jobs)

### AugentisLabs-Specific Validation

#### Quality Gate Validation (Constitution Principle II)
Phase gate thresholds that must be validated before progression:

- **VRC Gate (Venture Readiness Check)**: ≥76% score required to exit Discover phase
  - Validate all required evidence items present
  - Validate evidence tier ≥E2 for critical requirements
  - Validate market validation, problem statement, competitor analysis

- **VCD Gate (Venture Concept Definition)**: ≥75% score required to exit Define phase
  - Validate personas defined (≥2 required)
  - Validate PRD completeness (user stories, acceptance criteria)
  - Validate success metrics defined

- **DSP Gate (Design Specification Passing)**: ≥80% score required to exit Design phase
  - Validate wireframes complete for all critical flows
  - Validate system architecture diagram present
  - Validate API contracts (OpenAPI 3.0) defined
  - Validate data model (Prisma schema) complete

- **MDP Gate (Minimum Deliverable Product)**: ≥85% score required to exit Develop phase
  - Validate all P1 tests passing
  - Validate test coverage ≥80% for critical paths
  - Validate API contract tests passing (OpenAPI compliance)
  - Validate no critical security vulnerabilities (SAST passed)

**Gate Bypass Rules**:
- No phase bypass without written executive approval
- Risk assessment required for bypass
- Log all bypass attempts (approved or denied)

#### Evidence Tier Validation (Constitution Principle I)
Validate evidence tier classification for all requirements:

- **E0 (Assumption)**: No external validation; mark as high-risk
- **E1 (Anecdotal)**: Single source; require confirmation for critical decisions
- **E2 (Researched)**: Multiple sources; minimum for critical requirements ✅
- **E3 (Field-Tested)**: Prototype validated; acceptable for major features ✅
- **E4 (Production-Proven)**: Live usage data; gold standard ✅

**Validation Rules**:
- Critical requirements: Must have ≥E2 evidence
- P1 user stories: Must have ≥E2 evidence
- P2 user stories: E1 minimum acceptable
- P3 user stories: E0 acceptable
- Reject requirements with insufficient evidence tier

#### DTO Validation (NestJS)
Use class-validator decorators on all DTOs:

```typescript
export class CreateVentureDto {
  @IsString()
  @MinLength(3)
  @MaxLength(100)
  name: string;

  @IsString()
  @MinLength(10)
  @MaxLength(500)
  description: string;

  @IsEnum(VenturePhase)
  phase: VenturePhase;

  @IsOptional()
  @IsUUID()
  templateId?: string;
}
```

**Validation Pipeline**:
1. NestJS ValidationPipe transforms plain objects to DTO instances
2. class-validator validates decorators
3. Return 400 Bad Request with field-level errors if validation fails

#### Multi-Tenant Isolation Validation
Every request must validate tenant context:

```typescript
// NestJS Guard
@Injectable()
export class TenantGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const request = context.switchToHttp().getRequest();
    const tenantId = request.user?.tenantId;
    const resourceTenantId = request.params.tenantId;

    if (!tenantId) throw new UnauthorizedException('Tenant context missing');
    if (tenantId !== resourceTenantId) throw new ForbiddenException('Cross-tenant access denied');

    return true;
  }
}
```

**RLS Validation**:
- All Prisma queries must include tenant filter: `where: { tenantId: user.tenantId }`
- Never allow cross-tenant queries without explicit authorization
- Log all cross-tenant access attempts as security events

#### API Input Validation
- **UUID Validation**: All IDs must be valid UUIDs (@IsUUID())
- **Email Validation**: Use @IsEmail() for email fields
- **URL Validation**: Use @IsUrl() for external URLs
- **Date Validation**: Use @IsISO8601() for date fields
- **Enum Validation**: Use @IsEnum() for fixed sets (phases, evidence tiers, gate types)
- **Array Validation**: Use @IsArray(), @ArrayMinSize(), @ValidateNested()
- **Nested Object Validation**: Use @ValidateNested() with @Type() decorator

#### Frontend Validation (React + Zod)
Use Zod schemas for client-side validation:

```typescript
const ventureSchema = z.object({
  name: z.string().min(3).max(100),
  description: z.string().min(10).max(500),
  phase: z.enum(['DISCOVER', 'DEFINE', 'DESIGN', 'DEVELOP', 'DEPLOY']),
  templateId: z.string().uuid().optional(),
});

type VentureFormData = z.infer<typeof ventureSchema>;
```

**Form Validation with React Hook Form**:
```typescript
const { register, handleSubmit, formState: { errors } } = useForm<VentureFormData>({
  resolver: zodResolver(ventureSchema)
});
```

#### Business Rule Validation
Validate business rules in service layer:

- **Phase Progression**: Cannot skip phases; must complete gates in order (DISCOVER → DEFINE → DESIGN → DEVELOP → DEPLOY)
- **Agent Execution**: Cannot execute agents out of phase sequence
- **Artifact Versioning**: Version numbers must increment sequentially (v1 → v2 → v3)
- **User Story Dependencies**: Cannot mark story complete if dependencies incomplete
- **Cost Budget**: Cannot execute agents if tenant cost budget exceeded
- **Rate Limits**: Validate API rate limits per tenant (e.g., 1000 requests/hour)

#### Prisma Schema Validation
Use Prisma schema constraints:

```prisma
model Venture {
  id          String   @id @default(uuid())
  name        String   @db.VarChar(100)
  description String   @db.VarChar(500)
  tenantId    String   @db.Uuid
  phase       Phase    @default(DISCOVER)

  @@index([tenantId])
  @@unique([tenantId, name]) // Prevent duplicate venture names per tenant
}
```

#### File Upload Validation
- **File Size**: Max 10MB per upload
- **File Type**: Use allowlist (images: .png, .jpg, .svg; docs: .pdf, .md)
- **MIME Type Check**: Validate MIME type matches file extension
- **Virus Scan**: Scan uploads with ClamAV before storing in S3
- **Path Traversal Prevention**: Sanitize filenames; reject `../` patterns

#### Sanitization Rules
- **HTML Sanitization**: Use DOMPurify for user-generated HTML content
- **SQL Injection Prevention**: Use Prisma parameterized queries exclusively
- **XSS Prevention**: Escape output in React components (React does this by default)
- **Command Injection Prevention**: Never pass user input to shell commands
- **LDAP Injection Prevention**: Escape LDAP special characters if using LDAP

#### Performance Validation
- **API Response Time**: Validate <300ms P95 latency in integration tests
- **Frontend Performance**: Validate <2s LCP with Lighthouse CI
- **Database Query Performance**: Log slow queries (>100ms); optimize with indexes
- **Agent Execution Time**: Timeout agent execution after 5 minutes; use background jobs for long tasks
- **Token Usage**: Validate LLM token usage <5K per venture average

#### OpenAPI Contract Validation
- **Request Validation**: Validate all API requests match OpenAPI schema
- **Response Validation**: Validate all API responses match OpenAPI schema
- **Contract Tests**: Run contract tests in CI/CD pipeline
- **Breaking Changes**: Block deployment if API contract breaking changes detected (unless major version bump)

#### Traceability Validation (Constitution Principle V)
- **Requirement Traceability**: Validate every code module links back to a requirement
- **Test Traceability**: Validate every P1/P2 user story has passing tests
- **Artifact Versioning**: Validate artifacts versioned with full diffs (v1 → v2 → v3)
- **Cascade Impact**: When divergence detected, validate impact analysis completed before re-execution
