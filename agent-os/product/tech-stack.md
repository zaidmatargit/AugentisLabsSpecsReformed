# AugentisLabs Tech Stack

AugentisLabs technical stack - frozen per Constitution Principle VII. New dependencies require written CTO approval + Constitution amendment.

## Framework & Runtime
- **Backend Framework:** NestJS 10.x
- **Frontend Framework:** Next.js 14.x (App Router)
- **Language/Runtime:** TypeScript 5.x, Node.js LTS (v20.x)
- **Package Manager:** npm

## Frontend
- **JavaScript Framework:** React 18.x
- **CSS Framework:** Tailwind CSS
- **UI Components:** Custom component library
- **Performance Targets:** <2s LCP (Largest Contentful Paint)

## Database & Storage
- **Database:** PostgreSQL 15.x (Supabase managed)
- **ORM/Query Builder:** Prisma
- **Caching:** Redis (via Supabase)
- **File Storage:** AWS S3 (artifact exports)
- **Data Isolation:** Multi-tenant RLS (Row-Level Security)

## AI & Agent Orchestration
- **Agent Framework:** LangGraph (state persistence + error recovery)
- **LLM Providers:** OpenAI, Anthropic
- **Agent Architecture:** 26 specialized agents across 5D phases
- **Performance Targets:** ≥95% agent execution success rate, <5K tokens per venture average

## Testing & Quality
- **Test Framework:** Jest (unit tests), Supertest (API integration), Playwright (E2E)
- **Test Discipline:** TDD-first RED→GREEN→REFACTOR (failing tests before implementation)
- **Contract Testing:** OpenAPI 3.0 compliance validation
- **Linting/Formatting:** ESLint, Prettier
- **Quality Gates:** VRC ≥76%, VCD ≥75%, DSP ≥80%, MDP ≥85%

## Deployment & Infrastructure
- **Hosting:** Vercel (serverless functions + edge deployment)
- **Container Orchestration:** Docker, docker-compose (local development)
- **CI/CD:** GitHub Actions
- **Performance Targets:** API <300ms P95 latency

## Third-Party Services
- **Authentication:** Supabase Auth (JWT-based, multi-tenant)
- **Monitoring:** Sentry (error tracking), built-in telemetry (divergence detection)
- **Email:** SendGrid (notifications)
- **Security:** SAST (static analysis), data at rest encryption, GDPR compliance

## Architecture Patterns
- **Project Type:** SaaS web application (multi-tenant cloud-native)
- **Service Structure:** Monorepo with separate backend/frontend services
- **Data Governance:** Evidence tier classification (E0-E4), systematic traceability (PRD→Design→Code→Tests)
- **Scale Targets:** 1000+ concurrent users Month 12, 100+ ventures in development pipeline
