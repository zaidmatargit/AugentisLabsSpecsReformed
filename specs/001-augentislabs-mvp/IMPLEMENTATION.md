# Implementation Plan: AugentisLabs MVP Platform

**Branch**: `001-augentislabs-mvp` | **Date**: 2025-11-22 | **Spec**: [SPECIFICATION.md](./SPECIFICATION.md)
**Input**: Full-stack AI Software Factory with 5D methodology, 26 agents, multi-phase quality gates

---

## Summary

AugentisLabs is an AI-powered Software Factory that transforms startup ideas into production-ready applications through systematic 5D phases (Discover→Define→Design→Develop→Deploy) with 26 specialized agents and evidence-based quality gates. This implementation plan outlines the complete tech stack, project structure, and phase-gated development approach for the 16-week MVP timeline.

---

## Technical Context

**Language/Version**: TypeScript 5.x, Node.js LTS (v20.x)  
**Primary Dependencies**: NestJS (backend), Next.js 14.x (frontend), LangGraph (AI orchestration), Prisma (ORM), Supabase (PostgreSQL + Auth), Vercel (hosting)  
**Storage**: PostgreSQL 15.x via Supabase, AWS S3 for artifact exports  
**Testing**: Jest (unit), Supertest (integration), Playwright (E2E), contract tests with OpenAPI compliance  
**Target Platform**: Web (SaaS multi-tenant), cloud-native (serverless + managed services)  
**Project Type**: Web application (backend API + frontend SPA)  
**Performance Goals**: API <300ms P95 latency, frontend <2s LCP, agent execution success rate ≥95%  
**Constraints**: <5K LLM tokens per venture average, multi-tenant RLS isolation, GDPR compliance, zero critical security vulnerabilities  
**Scale/Scope**: 1000+ concurrent users by Month 12, 100+ ventures in pipeline, 26 AI agents, 5 quality gates

---

## Constitution Check

_GATE: Must pass before Phase 0 research. Re-check after Phase 1 design._

✅ **Evidence Tier Compliance**: All critical requirements classified E2+ (minimum for Define phase)

- User stories mapped to E2-E3 evidence (customer interviews, market research)
- VRC/VCD/DSP/MDP gate thresholds documented with evidence tier minimums
- Deploy telemetry collection enables E4 (production-proven) validation

✅ **Phase Gate Compliance**: 5D methodology with 4 mandatory gates (VRC ≥76%, VCD ≥75%, DSP ≥80%, MDP ≥85%)

- No phase can be skipped; Constitution blocks bypass attempts
- Quality/Safety/Cost/Strategic gate types with human approval authority
- Override protocol documented (max 7 days retroactive justification)

✅ **Test-First Discipline**: TDD mandatory, Red-Green-Refactor enforced

- Test Generator creates FAILING tests first (RED phase)
- Developers implement to GREEN, then refactor
- Contract tests validate OpenAPI spec compliance
- Integration tests cover all 5D phase workflows

✅ **User Story Independence**: Each P1/P2/P3 story independently implementable, testable, deployable

- Foundational Phase (authentication, database schema, API routing) blocks prerequisites
- Each user story delivers standalone value (Founder can stop at P1 and have viable MVP)
- Design: Story 1 (Discover), Story 2 (Define), Story 3 (Design), Story 4 (Develop), Story 5-6 (Deploy)

✅ **Stack Constraint Compliance**: Frozen MVP stack, no unapproved dependencies

- Backend: Node.js, TypeScript, NestJS, LangGraph
- Frontend: React 18.x, Next.js 14.x
- Database: PostgreSQL 15.x, Prisma ORM
- Testing: Jest, Supertest, Playwright
- Hosting: Vercel, Supabase

✅ **Traceability Enforcement**: PRD→Design→Code→Tests→Telemetry links documented

- Artifact versioning enabled (PRD v1→v2→v3)
- Cascade Impact Analyzer determines re-execution scope
- Divergence detection triggers selective updates (not full phase re-runs)

---

## Project Structure

### Documentation (Feature Specification Branch)

```
specs/001-augentislabs-mvp/
├── SPECIFICATION.md         # This spec (user stories, requirements, success criteria)
├── IMPLEMENTATION.md        # This file (tech stack, architecture, phase plan)
├── RESEARCH.md             # Phase 0: Competitor analysis, architectural patterns, LLM integration approaches
├── DATA-MODEL.md           # Phase 1: Database schema (Prisma), entity relationships, multi-tenant RLS policies
├── QUICKSTART.md           # Phase 1: Local development setup, Docker Compose, environment configuration
├── contracts/              # Phase 1: OpenAPI specs, GraphQL schemas, TypeScript interfaces
│   ├── api-discover.yaml
│   ├── api-define.yaml
│   ├── api-design.yaml
│   ├── api-develop.yaml
│   └── api-deploy.yaml
└── tasks.md                # Phase 2: Sprint breakdown (357 tasks, 7 layers, TDD-first)
```

### Source Code (Repository Root)

```
# Backend (NestJS)
backend/
├── src/
│   ├── main.ts                          # Application entry point
│   ├── app.module.ts                    # Root module with dependency injection
│   ├── config/                          # Environment configuration
│   │   ├── database.config.ts           # Supabase + Prisma
│   │   ├── llm.config.ts                # OpenAI + Anthropic API setup
│   │   └── auth.config.ts               # JWT + Supabase Auth
│   ├── modules/
│   │   ├── auth/                        # Multi-tenant auth + JWT tokens
│   │   ├── ventures/                    # Venture CRUD (create, read, update, delete)
│   │   ├── agents/                      # Agent orchestration (26 agents)
│   │   │   ├── discover/                # 4 Discover agents
│   │   │   ├── define/                  # 5 Define agents
│   │   │   ├── design/                  # 6 Design agents
│   │   │   ├── develop/                 # 7 Develop agents
│   │   │   └── deploy/                  # 4 Deploy agents
│   │   ├── gates/                       # Quality gate calculation (VRC/VCD/DSP/MDP)
│   │   ├── artifacts/                   # Artifact versioning + storage
│   │   ├── telemetry/                   # Divergence detection + telemetry
│   │   └── governance/                  # Audit logs, cost tracking, compliance
│   ├── services/
│   │   ├── llm.service.ts               # LLM API wrapper (retries, fallbacks)
│   │   ├── langgraph.service.ts         # LangGraph workflow orchestration
│   │   ├── evidence-tier.service.ts     # Evidence classification (E0-E4)
│   │   └── code-generator.service.ts    # Template-driven code generation
│   ├── entities/                        # Prisma-generated types
│   ├── filters/                         # Exception handling + logging
│   ├── guards/                          # Multi-tenant isolation guard
│   ├── pipes/                           # Request validation
│   └── interceptors/                    # Response transformation
├── prisma/
│   ├── schema.prisma                    # Database schema (Ventures, Artifacts, Telemetry, AuditLogs)
│   └── migrations/                      # Database migration history
├── tests/
│   ├── contract/                        # OpenAPI spec compliance tests
│   │   ├── discover.contract.test.ts
│   │   ├── define.contract.test.ts
│   │   ├── design.contract.test.ts
│   │   ├── develop.contract.test.ts
│   │   └── deploy.contract.test.ts
│   ├── integration/                     # User journey tests (full 5D workflow)
│   │   ├── discover-phase.integration.test.ts
│   │   ├── define-phase.integration.test.ts
│   │   ├── design-phase.integration.test.ts
│   │   ├── develop-phase.integration.test.ts
│   │   └── deploy-phase.integration.test.ts
│   └── unit/                            # Service unit tests
│       ├── gates.service.test.ts
│       ├── evidence-tier.service.test.ts
│       └── agent-orchestration.service.test.ts
├── docker-compose.yml                   # PostgreSQL + Redis for local dev
└── package.json                         # Dependencies: NestJS, Prisma, LangGraph, OpenAI, Anthropic

# Frontend (Next.js)
frontend/
├── app/
│   ├── layout.tsx                       # Root layout + auth provider
│   ├── page.tsx                         # Landing page
│   ├── dashboard/
│   │   ├── layout.tsx                   # Dashboard layout (sidebar nav)
│   │   ├── page.tsx                     # Dashboard home
│   │   ├── ventures/
│   │   │   ├── [id]/
│   │   │   │   ├── discover/            # Discover phase UI
│   │   │   │   ├── define/              # Define phase UI
│   │   │   │   ├── design/              # Design phase UI
│   │   │   │   ├── develop/             # Develop phase UI
│   │   │   │   └── deploy/              # Deploy phase UI
│   │   │   └── create/                  # Venture creation form
│   │   ├── artifacts/
│   │   │   ├── [type]/                  # PRD, personas, wireframes, code
│   │   │   └── [id]/view                # Artifact viewer
│   │   ├── gates/                       # Quality gate dashboard
│   │   │   ├── vrc/
│   │   │   ├── vcd/
│   │   │   ├── dsp/
│   │   │   └── mdp/
│   │   ├── telemetry/                   # Deploy phase telemetry dashboards
│   │   ├── divergence/                  # Divergence detector + alerts
│   │   └── settings/                    # Account + org settings
│   └── api/                             # Next.js API routes (wrapper → NestJS backend)
├── components/
│   ├── common/                          # Reusable: Header, Footer, Sidebar, Modal, Button
│   ├── ventures/                        # Venture-specific components
│   ├── gates/                           # Gate decision UI (PASS/CONDITIONAL/FAIL)
│   ├── telemetry/                       # Charts, graphs, usage analytics
│   └── forms/                           # Form components with validation
├── hooks/
│   ├── useVenture.ts                    # Fetch venture data, mutations
│   ├── useFetchArtifact.ts              # Fetch + cache artifacts
│   ├── useGateDecision.ts               # Monitor gate status
│   └── useTelemetry.ts                  # Real-time telemetry subscription
├── lib/
│   ├── api-client.ts                    # Axios wrapper to NestJS backend
│   ├── auth.ts                          # Supabase Auth client
│   └── utils.ts                         # Utility functions
├── styles/
│   ├── globals.css                      # Tailwind CSS + custom styles
│   └── components.module.css             # Component-specific styles
├── tests/
│   ├── e2e/                             # Playwright E2E tests
│   │   ├── discover-flow.spec.ts        # Submit idea → receive VRC score
│   │   ├── full-5d-flow.spec.ts         # Complete Discover→Deploy journey
│   │   └── gates.spec.ts                # Quality gate interactions
│   └── unit/                            # Component + hook tests
│       ├── components.test.tsx
│       └── hooks.test.ts
├── middleware.ts                        # Authentication middleware
├── tsconfig.json                        # TypeScript configuration
├── next.config.js                       # Next.js configuration
├── package.json                         # Dependencies: React, Next.js, Tailwind, SWR
└── .env.local                           # Local environment (API URL, keys)

# Shared Configuration
├── .github/
│   ├── workflows/
│   │   ├── ci.yml                       # PR: lint, test, build
│   │   ├── deploy-staging.yml           # Merge to main → Deploy to staging
│   │   └── deploy-prod.yml              # Release tag → Deploy to production
│   └── CODEOWNERS                       # Code review assignments
├── docker-compose.yml                   # Full stack (PostgreSQL, Redis, backend, frontend)
├── Makefile                             # Development commands: make dev, make test, make build
├── .eslintrc.json                       # Lint rules (TypeScript strict)
├── .prettierrc                          # Code formatting
├── .env.example                         # Environment template
└── README.md                            # Project setup guide
```

---

## Complexity Tracking

| Violation                            | Why Needed                                                      | Simpler Alternative Rejected Because                                                                              |
| ------------------------------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| 26 AI agents                         | Specialized domain agents improve output quality                | Single monolithic agent produces lower-quality artifacts, e.g., generic wireframes that don't align with personas |
| LangGraph workflow engine            | Stateful orchestration enables error recovery, retry logic      | Simple sequential API calls fail if agent times out, no resumability                                              |
| Multi-tenant RLS policies            | GDPR compliance, customer data isolation, audit trails          | Single-tenant architecture doesn't scale to enterprise segment, compliance risk                                   |
| 5D phases + 4 gates                  | Systematic validation prevents wasted dev effort                | Skipping phases leads to 90% venture failure rate (industry baseline)                                             |
| Evidence tier classification (E0-E4) | Risk management - prevents decisions on unvalidated assumptions | Making decisions on E0 guesses leads to market misalignment, downstream waste                                     |

---

## Development Phases

### Phase 0: Research & Architecture (Weeks 1-2)

**Objectives**: Validate architectural patterns, LLM integration approaches, and quality gate calculation logic

**Key Activities**:

1. Research comparable systems: No-code platforms (Bubble, FlutterFlow), AI code generators (GitHub Copilot, Replit), product development tools (Figma, Penpot)
2. Study LangGraph workflow patterns: State management, error handling, multi-agent coordination
3. Design evidence tier calculation algorithm: How to score E0-E4 for 20 VRC indicators
4. Design cascade impact analyzer: How to determine minimum re-execution scope for divergence-triggered updates
5. Benchmark LLM APIs: Cost per token, latency, quality for different model combinations

**Deliverables**: RESEARCH.md with architectural decisions documented

### Phase 1: Foundation Setup (Weeks 3-4)

**Objectives**: Project scaffolding, database schema, API contracts, local development environment

**Key Activities**:

1. Initialize NestJS backend + Next.js frontend with TypeScript strict mode
2. Design Prisma schema: Ventures, Artifacts, Telemetry, AuditLogs, Users, Organizations (multi-tenant)
3. Setup Supabase: PostgreSQL database, Row-Level Security policies, Auth integration
4. Write OpenAPI 3.0 specifications for all 5D phase APIs (Discover, Define, Design, Develop, Deploy)
5. Setup testing infrastructure: Jest configuration, Supertest setup, contract test templates
6. Configure CI/CD: GitHub Actions for lint, test, build on every PR

**Deliverables**: DATA-MODEL.md, OpenAPI specs, Docker Compose, QUICKSTART.md

### Phase 2: Agent Orchestration & Gates (Weeks 5-7)

**Objectives**: LangGraph workflows, 26 agent integration, VRC/VCD/DSP/MDP calculation

**Key Activities**:

1. Implement LangGraph workflow engine: State management, error handling, retry logic
2. Integrate LLM APIs (OpenAI GPT-4 Turbo + Anthropic Claude 3.5 Sonnet) with fallback handling
3. Implement 4 Discover agents: Problem Validator, Research Scout, Signal Miner, Opportunity Scorer
4. Implement VRC calculation: 20-indicator assessment with evidence tier weighting
5. Write contract tests validating OpenAPI spec compliance for all agents
6. Implement quality gate endpoints: GET /ventures/{id}/vrc, POST /ventures/{id}/vrc/decision

**Deliverables**: Discover phase fully functional with VRC gate operational

### Phase 3: Define Phase & Market Intelligence (Weeks 8-10)

**Objectives**: Market research agents, persona generation, PRD creation, VCD gate

**Key Activities**:

1. Implement 5 Define agents: Market Research, Persona Builder, Interview Ingestion, Requirements Generator, Feasibility Analyzer
2. Build document ingestion: PDF parsing, transcript extraction, structured data classification
3. Implement VCD calculation: 10-indicator assessment validating market research quality
4. Build artifact versioning system: Track PRD v1→v2→v3 with full diffs
5. Implement traceability graph: Link user stories → design screens → code modules → tests
6. Write integration tests for full Discover→Define workflow

**Deliverables**: Define phase fully functional with VCD gate operational

### Phase 4: Design Phase & Architecture (Weeks 11-13)

**Objectives**: UX/UI generation, design system, API specification, DSP gate

**Key Activities**:

1. Implement 6 Design agents: UX Agent, Branding Agent, Journey Map Agent, Architecture Agent, Prototyper, DSP Assembler
2. Build wireframe generation from user stories (80%+ coverage target)
3. Design system library generation: Colors, typography, spacing, 50+ components
4. C4 architecture diagram generation + OpenAPI 3.0 spec from requirements
5. Implement DSP calculation: 8-component assessment validating design completeness
6. Write integration tests for Discover→Design workflow

**Deliverables**: Design phase fully functional with DSP gate operational

### Phase 5: Code Generation & Testing (Weeks 14-17)

**Objectives**: Backend/frontend code generation, test scaffolding, security scanning, MDP gate

**Key Activities**:

1. Implement 7 Develop agents: Code Generator, Schema Generator, Test Generator, Frontend Generator, Documentation Generator, Security Scanner, Performance Optimizer
2. Build template engine: Generate NestJS endpoints from OpenAPI spec, Prisma schema from ERD, Next.js components from wireframes
3. Implement test-first discipline: Generate FAILING tests (RED phase) first, developer implements to GREEN, refactor
4. Integrate security scanning: SAST (SonarQube), dependency scanning (npm audit), container scanning
5. Implement performance profiling: Identify bottlenecks, generate optimization recommendations
6. Implement MDP calculation: 6-component assessment validating production readiness
7. Write full integration tests: Discover→Deploy complete workflow

**Deliverables**: Develop phase fully functional with MDP gate operational, generated MVP applications ready for beta

### Phase 6: Deploy & Feedback Loops (Weeks 18-19)

**Objectives**: Production deployment, telemetry collection, divergence detection, cascade re-execution

**Key Activities**:

1. Implement 4 Deploy agents: Telemetry Aggregator, Divergence Detector, Define-Update Proposer, Cascade Impact Analyzer
2. Setup deployment infrastructure: Vercel frontend, Supabase backend, S3 artifact storage
3. Build telemetry dashboards: Feature usage, user cohorts, retention curves, crash rates, P95 latency
4. Implement divergence detection: Compare Define assumptions (E1-E2) vs Deploy telemetry (E4)
5. Implement cascade impact analysis: Which Design screens affected, which Code modules affected, re-execution effort estimate
6. Build governance dashboard: Decision audit trail, cost tracking, compliance checklist
7. Setup monitoring + alerting: Error tracking (Sentry), performance monitoring (Vercel Analytics)

**Deliverables**: Deploy phase fully functional, first customers in production with feedback loops operational

### Phase 7: Beta Launch & Iteration (Weeks 20-22)

**Objectives**: Beta customer onboarding, feedback collection, performance optimization

**Key Activities**:

1. Onboard 10-20 beta customers (early-stage founders + venture studios)
2. Collect feedback on each 5D phase: Which agents produce highest-quality artifacts? Where is friction?
3. Iterate on agent prompts: A/B test different templates, models, sampling strategies
4. Optimize LLM costs: Reduce average tokens per venture while maintaining quality
5. Performance tuning: Reduce agent execution time, API latency optimization
6. Launch marketing: Landing page, case studies, founder testimonials

**Deliverables**: Production-ready platform, paying customers, positive NPS ≥40

---

## Tech Stack Rationale

**Backend: NestJS**

- Rationale: Enterprise-grade Node.js framework with dependency injection (critical for testing, mock LLM APIs), modular architecture, built-in validation, middleware support
- Alternatives rejected: Express (too bare-bones, no DI), Fastify (less ecosystem), Django/Flask (introduces Python dependency)

**Frontend: Next.js 14 + React 18**

- Rationale: Server-side rendering capability reduces initial load time, API routes provide simple backend integration point, streaming support for real-time updates
- Alternatives rejected: SPA frameworks (Vite/React Router - no SSR), Remix (learning curve)

**Database: PostgreSQL 15 via Supabase**

- Rationale: Relational model perfect for artifact versioning + traceability, Row-Level Security enables multi-tenant isolation, Supabase manages infrastructure
- Alternatives rejected: MongoDB (document model doesn't suit relational artifact structure), Firebase (limited query flexibility for complex gate calculations)

**AI Orchestration: LangGraph**

- Rationale: State management, error recovery, human-in-the-loop gates, workflow visualization
- Alternatives rejected: LangChain (lower-level abstractions, no native state), custom orchestration (high maintenance burden)

**Testing: Jest + Supertest + Playwright**

- Rationale: Jest (industry standard unit tests), Supertest (API testing), Playwright (browser automation for E2E)
- Alternatives rejected: Vitest (smaller ecosystem), Mocha/Chai (more boilerplate), Cypress (E2E only)

**Hosting: Vercel + Supabase**

- Rationale: Vercel (zero-config Next.js deployment, edge functions), Supabase (managed PostgreSQL + auth)
- Alternatives rejected: AWS/Azure/GCP (complexity, higher ops burden for MVP), Heroku (higher cost at scale)

---

## Quality Assurance Strategy

**Testing Pyramid**:

1. **Unit Tests (Jest)**: Service logic, evidence tier calculation, gate scoring algorithms
2. **Contract Tests (Supertest)**: OpenAPI spec compliance for all agents
3. **Integration Tests (Supertest)**: Full 5D workflows (Discover→Define→Design→Develop→Deploy)
4. **E2E Tests (Playwright)**: User journeys (create venture → submit idea → receive VRC score → etc)

**Gate Validation**:

- VRC score calculation: Compare human expert assessment vs system score on sample ventures
- Code generation quality: Run generated code through Lighthouse, security scans, performance profilers
- Artifact versioning: Track diffs between PRD v1→v2, ensure traceability maintained

**Monitoring**:

- Agent success rate: Track execution failures, log failures with telemetry for analysis
- LLM cost tracking: Monitor tokens per venture, forecast spending vs budget
- API latency: Track p50/p95/p99 latency for each agent API
- User satisfaction: NPS surveys at each phase completion gate

---

## Risk Mitigation

| Risk                              | Likelihood | Impact                      | Mitigation                                                                      |
| --------------------------------- | ---------- | --------------------------- | ------------------------------------------------------------------------------- |
| LLM quality inconsistent          | High       | Code/spec unusable          | A/B test prompts, fallback to human review if confidence <70%, version prompts  |
| LLM API cost spirals              | Medium     | Budget blown                | Token tracking, per-venture cap ($5K), cost gate alerts                         |
| Multi-tenant isolation breach     | Low        | Data leak, GDPR violation   | Supabase RLS policies, automated compliance audit, penetration testing          |
| Agent execution failure           | Medium     | Customer stuck              | Retry logic (exponential backoff), human escalation, queue for manual review    |
| Phase gate calculated incorrectly | Low        | Product unsuitable released | Contract tests validate calculation, manual spot checks, customer feedback loop |

---

## Success Metrics (Phase Gates)

✅ **Phase 0 Complete**: Architecture docs, LLM integration approach validated, gate calculation algorithms designed  
✅ **Phase 1 Complete**: Database schema deployed, API contracts written, local dev environment functional  
✅ **Phase 2 Complete**: Discover agents operational, VRC gate calculating correctly, contract tests passing  
✅ **Phase 3 Complete**: Define agents operational, VCD gate calculating correctly, traceability graph working  
✅ **Phase 4 Complete**: Design agents operational, DSP gate calculating correctly, wireframes covering 80%+ of stories  
✅ **Phase 5 Complete**: Develop agents operational, MDP gate calculating correctly, generated code passing security scan  
✅ **Phase 6 Complete**: Deploy phase operational, first customers in production, telemetry dashboards live  
✅ **Phase 7 Complete**: 10-20 beta customers, positive NPS ≥40, monthly churn <5%

---

## Next Steps

1. **Phase 0 (Week 1-2)**: Create RESEARCH.md with architectural validation
2. **Phase 1 (Week 3-4)**: Create DATA-MODEL.md, deploy Supabase, write OpenAPI specs
3. **Phases 2-7**: Follow implementation plan above, updating IMPLEMENTATION.md with progress
