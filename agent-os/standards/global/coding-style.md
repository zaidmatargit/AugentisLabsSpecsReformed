## Coding style best practices

### General Principles
- **Consistent Naming Conventions**: Follow TypeScript/JavaScript conventions - camelCase for variables/functions, PascalCase for classes/components, UPPER_SNAKE_CASE for constants
- **Automated Formatting**: ESLint + Prettier configured project-wide - code must pass linting before commits
- **Meaningful Names**: Choose descriptive names that reveal intent; avoid abbreviations except standard domain terms (e.g., LLM, RLS, VRC)
- **Small, Focused Functions**: Keep functions small and focused on a single task for better readability and testability
- **Consistent Indentation**: 2 spaces for TypeScript/JavaScript (enforced by Prettier)
- **Remove Dead Code**: Delete unused code, commented-out blocks, and imports rather than leaving them as clutter
- **Backward Compatibility**: Unless specifically instructed otherwise, assume you do not need to write additional code logic for backward compatibility
- **DRY Principle**: Avoid duplication by extracting common logic into reusable functions or modules

### AugentisLabs-Specific Standards

#### Evidence Tier Compliance (Constitution Principle I)
- **Critical Decisions**: Must be documented with evidence tier classification (E0-E4)
- **E2+ Minimum**: All critical requirements must have E2+ evidence (customer interviews + market research)
- **Traceability**: Maintain links from PRD → Design → Code → Tests → Telemetry
- **Decision Artifacts**: Log evidence tier in code comments for architectural decisions

#### Test-Driven Development (Constitution Principle III)
- **RED→GREEN→REFACTOR**: Write FAILING tests first (RED), then implement to pass (GREEN), then refactor
- **No Implementation Before Tests**: Test Generator creates failing tests before any code is written
- **Test Coverage**: Aim for ≥80% coverage on critical paths (5D phases, quality gates, multi-tenant isolation)
- **Test Organization**: Unit tests alongside source files, integration tests in `tests/integration/`, E2E in `tests/e2e/`

#### Multi-Tenant Patterns
- **RLS Enforcement**: All database queries must respect PostgreSQL Row-Level Security policies
- **Tenant Context**: Every request must include tenant/venture context in headers or JWT claims
- **Data Isolation Guards**: Use NestJS guards to validate tenant isolation before controller execution
- **No Cross-Tenant Queries**: Never allow queries that span multiple tenants without explicit authorization

#### LangGraph Agent Patterns
- **State Persistence**: Agent state must be persisted to database for error recovery
- **Error Recovery**: Implement retry logic with exponential backoff for LLM calls
- **Token Efficiency**: Target <5K tokens per venture average; use prompt caching where possible
- **Success Rate Monitoring**: Track agent execution success rate (target ≥95%)

#### Performance Standards
- **API Response Time**: <300ms P95 latency for all endpoints
- **Frontend Performance**: <2s LCP (Largest Contentful Paint)
- **Database Queries**: Use Prisma query optimization; avoid N+1 queries
- **Async Operations**: Use background jobs for long-running tasks (agent execution, artifact generation)

#### Security Standards
- **Input Validation**: Validate all user inputs using NestJS pipes and class-validator
- **SQL Injection Prevention**: Use Prisma parameterized queries exclusively (no raw SQL without review)
- **XSS Prevention**: Sanitize all user-generated content before rendering in React components
- **GDPR Compliance**: Implement data at rest encryption; support data export/deletion requests
- **SAST Requirements**: All code must pass static analysis security testing before merge

#### File Organization
- **NestJS Modules**: One feature per module (e.g., `ventures.module.ts`, `agents.module.ts`)
- **Service Layer**: Business logic in services, not controllers
- **DTO Pattern**: Use Data Transfer Objects for all API requests/responses
- **Prisma Entities**: Define entities in `prisma/schema.prisma`, generate types with `prisma generate`
- **Next.js App Router**: Use `app/` directory structure with server/client components clearly marked

#### TypeScript Standards
- **Strict Mode**: Enable `strict: true` in tsconfig.json
- **Explicit Types**: Prefer explicit type annotations over inference for public APIs
- **Avoid `any`**: Use `unknown` or proper types instead of `any`
- **Enums for Constants**: Use TypeScript enums for fixed sets of values (e.g., evidence tiers, phase names)
- **Interface over Type**: Prefer interfaces for object shapes, type aliases for unions/primitives
