# Implementation Plan: AugentisLabs MVP Platform

**Branch**: `001-augentislabs-mvp` | **Date**: 2025-11-22 | **Spec**: [SPECIFICATION.md](./SPECIFICATION.md)
**Input**: Full-stack AI Software Factory with 5D methodology, 26 agents, multi-phase quality gates

**Note**: This plan continues from Constitution v1.0.0 (governance framework established). Phase 0 research validates architectural decisions documented in IMPLEMENTATION.md.

## Summary

AugentisLabs is an AI-powered Software Factory that transforms startup ideas into production-ready applications through systematic 5D phases (Discover→Define→Design→Develop→Deploy) with 26 specialized agents and evidence-based quality gates. MVP delivers: Discover agent system with VRC quality gate, Define phase with personas + PRD generation, Design phase with wireframes + architecture, Develop phase with code generation + TDD, Deploy phase with telemetry + divergence detection, and Governance oversight dashboard. 16-week timeline to production MVP supporting 3 pricing tiers ($499-Custom/month).

## Technical Context

**Language/Version**: TypeScript 5.x, Node.js LTS (v20.x)  
**Primary Dependencies**: NestJS 10.x (backend), Next.js 14.x (frontend), LangGraph (agent orchestration), Prisma (ORM), Supabase (managed PostgreSQL + Auth), Vercel (serverless hosting)  
**Storage**: PostgreSQL 15.x (via Supabase), AWS S3 (artifact exports), Supabase Auth (user management)  
**Testing**: Jest (unit tests), Supertest (API integration), Playwright (E2E), OpenAPI contract tests, TDD-first discipline (failing tests RED phase before GREEN implementation)  
**Target Platform**: SaaS web application (multi-tenant cloud-native)
**Project Type**: Web application (NestJS backend + Next.js frontend, separate services)  
**Performance Goals**: API <300ms P95 latency, frontend <2s LCP, agent execution success rate ≥95%, <5K LLM tokens per project average  
**Constraints**: Multi-tenant RLS data isolation, GDPR compliance (data at rest encryption), zero critical security vulnerabilities (SAST passed), evidence tier classification E2+ for all critical decisions  
**Scale/Scope**: 1000+ concurrent users Month 12, 100+ projects in development pipeline, 26 specialized AI agents, 5D phases, 4 quality gates (VRC/VCD/DSP/MDP), 3 pricing tiers, 22-week development timeline

## Constitution Check

_GATE: Must pass before Phase 0 research. Re-check after Phase 1 design._

✅ **Evidence Tier Compliance (Principle I)**: All critical requirements classified E2+ minimum

- User stories mapped to E2-E3 evidence (customer interviews + market research documented in product brief)
- VRC/VCD/DSP/MDP gate thresholds with E2+ minimum evidence requirements
- Deploy phase telemetry collection enables E4 (production-proven) validation

✅ **Phase Gate Compliance (Principle II)**: 5D phases with 4 mandatory quality gates

- VRC gate ≥76% (Discover phase completion)
- VCD gate ≥75% (Define phase completion)
- DSP gate ≥80% (Design phase completion)
- MDP gate ≥85% (Develop phase completion)
- No phase bypass without written executive approval + risk assessment

✅ **Test-First Discipline (Principle III)**: TDD enforced RED→GREEN→REFACTOR

- Test Generator creates FAILING tests first (RED) before implementation
- Developers implement to pass tests (GREEN), then refactor
- Contract tests validate OpenAPI 3.0 compliance
- Integration tests cover complete 5D phase workflows

✅ **User Story Independence (Principle VI)**: Each P1/P2 story independently deployable

- Story 1 (Discover) + Story 2 (Define) + Story 3 (Design) can be deployed as MVP checkpoint
- Story 4 (Develop) adds code generation capability
- Stories 5-6 (Deploy + Governance) add production telemetry
- Each story independently testable with separate test suites

✅ **Stack Constraint Compliance (Principle VII)**: Frozen stack, no unapproved dependencies

- Backend: Node.js LTS, TypeScript 5.x, NestJS 10.x, LangGraph, Prisma
- Frontend: React 18.x, Next.js 14.x, Tailwind CSS
- Database: PostgreSQL 15.x, Supabase (managed)
- Testing: Jest, Supertest, Playwright
- New dependencies require written CTO approval + Constitution amendment

✅ **Systematic Traceability (Principle V)**: PRD→Design→Code→Tests→Telemetry links

- Artifact versioning: PRD v1→v2→v3 tracked with full diffs
- Traceability graph: User story ← requirement ← design screen ← code module ← tests
- Cascade Impact Analyzer: Given divergence, identify affected components
- Deploy phase divergence detection triggers selective re-execution (not full redesign)

## Project Structure

### Documentation (this feature)

```
specs/master/
├── plan.md              # This file - implementation plan
├── research.md          # Phase 0: Architecture validation & LLM patterns
├── data-model.md        # Phase 1: Complete Prisma schema with RLS policies
├── quickstart.md        # Phase 1: Local development setup guide
├── contracts/           # Phase 1: OpenAPI 3.0 specifications
│   ├── api-discover.yaml   # Discover phase endpoints
│   ├── api-define.yaml     # Define phase endpoints
│   ├── api-design.yaml     # Design phase endpoints
│   ├── api-develop.yaml    # Develop phase endpoints
│   └── api-deploy.yaml     # Deploy phase + governance endpoints
└── tasks.md             # Phase 2: 357 implementation tasks (TDD-first)
```

### Source Code (repository root)

Selected: **Option 2 - Web Application (NestJS backend + Next.js frontend)**

```
augentislabs/
├── backend/                           # NestJS API server (Node.js LTS + TypeScript 5.x)
│   ├── src/
│   │   ├── main.ts                   # Application entry point
│   │   ├── app.module.ts             # Root module with dependency injection
│   │   ├── config/                   # Environment configuration
│   │   │   ├── database.config.ts    # Supabase PostgreSQL + Prisma
│   │   │   ├── llm.config.ts         # OpenAI + Anthropic API setup
│   │   │   └── auth.config.ts        # JWT + Supabase Auth
│   │   ├── modules/                  # Feature modules
│   │   │   ├── auth/                 # Multi-tenant authentication
│   │   │   ├── projects/             # Project CRUD operations
│   │   │   ├── agents/               # 26 AI agents orchestration
│   │   │   │   ├── discover/         # 4 Discover phase agents
│   │   │   │   ├── define/           # 5 Define phase agents
│   │   │   │   ├── design/           # 6 Design phase agents
│   │   │   │   ├── develop/          # 7 Develop phase agents
│   │   │   │   └── deploy/           # 4 Deploy phase agents
│   │   │   ├── gates/                # Quality gate calculation
│   │   │   ├── artifacts/            # Artifact versioning & storage
│   │   │   ├── telemetry/            # Divergence detection
│   │   │   └── governance/           # Audit logs, cost tracking
│   │   ├── services/
│   │   │   ├── llm.service.ts        # LLM wrapper with retry/fallback
│   │   │   ├── langgraph.service.ts  # LangGraph orchestration
│   │   │   ├── evidence-tier.service.ts
│   │   │   └── code-generator.service.ts
│   │   ├── entities/                 # Database entity types
│   │   ├── filters/                  # Exception handling
│   │   ├── guards/                   # Multi-tenant isolation
│   │   ├── pipes/                    # Request validation
│   │   └── interceptors/             # Response transformation
│   ├── prisma/
│   │   ├── schema.prisma             # Complete database schema
│   │   └── migrations/               # Migration history
│   ├── tests/
│   │   ├── contract/                 # OpenAPI compliance tests
│   │   ├── integration/              # Full workflow tests
│   │   └── unit/                     # Service unit tests
│   ├── docker-compose.yml            # PostgreSQL + Redis + pgAdmin
│   ├── package.json
│   └── jest.config.js

├── frontend/                         # Next.js 14 React application
│   ├── app/
│   │   ├── layout.tsx                # Root layout + auth provider
│   │   ├── page.tsx                  # Landing page
│   │   ├── dashboard/
│   │   │   ├── layout.tsx            # Dashboard layout (sidebar nav)
│   │   │   ├── page.tsx              # Dashboard home
│   │   │   ├── projects/
│   │   │   │   ├── [id]/
│   │   │   │   │   ├── discover/     # Discover phase UI
│   │   │   │   │   ├── define/       # Define phase UI
│   │   │   │   │   ├── design/       # Design phase UI
│   │   │   │   │   ├── develop/      # Develop phase UI
│   │   │   │   │   └── deploy/       # Deploy phase UI
│   │   │   │   └── create/           # Project creation
│   │   │   ├── artifacts/            # Artifact viewer
│   │   │   ├── gates/                # Quality gate dashboard
│   │   │   ├── telemetry/            # Deploy dashboards
│   │   │   ├── divergence/           # Divergence alerts
│   │   │   └── settings/             # Account settings
│   │   └── api/                      # Next.js API routes (proxy)
│   ├── components/
│   │   ├── common/                   # Reusable UI components
│   │   ├── projects/                 # Project-specific components
│   │   ├── gates/                    # Gate decision UI
│   │   ├── telemetry/                # Charts & visualizations
│   │   └── forms/                    # Form components
│   ├── hooks/
│   │   ├── useProject.ts
│   │   ├── useFetchArtifact.ts
│   │   ├── useGateDecision.ts
│   │   └── useTelemetry.ts
│   ├── lib/
│   │   ├── api-client.ts             # Axios wrapper
│   │   ├── auth.ts                   # Supabase Auth
│   │   └── utils.ts
│   ├── styles/
│   │   ├── globals.css               # Tailwind CSS
│   │   └── components.module.css
│   ├── tests/
│   │   └── e2e/                      # Playwright E2E tests
│   ├── package.json
│   └── next.config.js

├── .specify/                         # Speckit governance
│   ├── memory/
│   │   └── constitution.md           # v1.0.0 - Non-negotiable principles
│   └── scripts/
│       └── powershell/
│           ├── setup-plan.ps1
│           └── update-agent-context.ps1

├── docker-compose.yml                # Multi-service orchestration
├── Makefile                          # Development commands
├── .env.example                      # Environment template
├── README.md                         # Project overview
└── ARCHITECTURE.md                   # High-level system design
```

**Structure Decision**: Option 2 selected (NestJS backend + Next.js frontend) because:

- Separates concerns: Backend handles AI orchestration, database, LLM integration
- Frontend handles UI/UX with Next.js App Router, SSR for SEO
- Scalability: Backend on serverless (Vercel), frontend on Vercel edge
- Testing: Contract tests validate API, E2E tests validate workflows
- Deployment: Independent scaling of services

## Complexity Tracking

Constitution Check: All 6 principles verified ✅ - No violations requiring justification

| Decision                    | Complexity | Why This Approach                                                                 |
| --------------------------- | ---------- | --------------------------------------------------------------------------------- |
| **26 AI Agents**            | High       | Required for 5D methodology; monolithic agent insufficient                        |
| **LangGraph Orchestration** | High       | State persistence essential for error recovery                                    |
| **Multi-Tenant RLS**        | High       | PostgreSQL RLS necessary for GDPR; app-layer filtering error-prone                |
| **Evidence Tier Algorithm** | Medium     | 5-level classification (E0-E4) required for risk management                       |
| **Cascade Impact Analyzer** | High       | Traceability graph essential for selective re-execution vs 3-4 week full redesign |

