<!--
═══════════════════════════════════════════════════════════════════════════════
SYNC IMPACT REPORT
═══════════════════════════════════════════════════════════════════════════════
Version Change: TEMPLATE → 1.0.0 (Initial Constitution)

Modified Principles:
- NEW: I. Evidence-Based Development (E0-E4 classification system)
- NEW: II. Phase-Gated Quality Control (5D methodology with quality gates)
- NEW: III. Test-First Discipline (TDD mandatory, Red-Green-Refactor)
- NEW: IV. AI-Human Partnership (AI for automation, humans for judgment)
- NEW: V. Systematic Traceability (PRD→Design→Code→Tests→Telemetry)
- NEW: VI. User Story Independence (each story independently deployable)
- NEW: VII. Stack Constraint Discipline (no new dependencies without approval)

Added Sections:
- Technology Stack Constraints (Node.js/TypeScript, Next.js, Supabase, Vercel)
- Development Workflow (Constitution Check in plan-template.md)
- Quality Gates enforcement (VRC, VCD, DSP, MDP thresholds)

Templates Requiring Updates:
✅ plan-template.md - Constitution Check section aligns
✅ spec-template.md - User story independence aligns
✅ tasks-template.md - Test-first and story independence aligns

Follow-up TODOs:
- None - all placeholders resolved
═══════════════════════════════════════════════════════════════════════════════
-->

# AugentisLabs Platform Constitution

## Core Principles

### I. Evidence-Based Development

Every decision, requirement, and artifact MUST be classified by evidence tier (E0-E4). No critical decisions may proceed based solely on E0 (unvalidated assumptions). Phase gates enforce minimum evidence thresholds:

- **E0 (Unvalidated)**: Raw hypotheses, 0-20% confidence - Not acceptable for critical decisions
- **E1 (Exploratory)**: Initial exploration, 20-40% confidence - Acceptable for early prototyping only
- **E2 (Corroborated)**: Multiple sources align, 40-70% confidence - Minimum for Define phase
- **E3 (Expert-Verified)**: Industry expert validated, 70-85% confidence - Preferred for Design phase
- **E4 (Production-Proven)**: Real telemetry from ≥1K users, 85-100% confidence - Deploy phase standard

**Rationale**: The AugentisLabs 5D methodology systematically increases evidence quality from Discover (E1-E2) through Deploy (E4), reducing venture failure risk from 90% to <70%.

### II. Phase-Gated Quality Control

Development MUST progress through 5D phases (Discover→Define→Design→Develop→Deploy) with mandatory quality gates. No phase may be skipped or bypassed without written executive approval and risk assessment.

**Quality Gate Thresholds**:

- **VRC (Venture Readiness Compass)**: ≥76% required to proceed from Discover to Define
- **VCD (Venture Competitiveness Dashboard)**: ≥75% required to proceed from Define to Design
- **DSP (Design Specification Passed)**: ≥80% required to proceed from Design to Develop
- **MDP (Milestone Delivery Readiness)**: ≥85% required to proceed from Develop to Deploy

**Rationale**: Phase gates prevent premature progression, technical debt accumulation, and market misalignment. Each gate validates that prior phase deliverables meet quality standards before resource investment in subsequent phases.

### III. Test-First Discipline (NON-NEGOTIABLE)

Test-Driven Development (TDD) is mandatory for all features. The Red-Green-Refactor cycle MUST be strictly enforced:

1. **Red**: Write test(s) for user story acceptance criteria
2. **User Approval**: Present failing tests to stakeholder for validation
3. **Green**: Implement minimal code to make tests pass
4. **Refactor**: Improve code quality while maintaining passing tests

**Contract Tests Required For**:

- All API endpoints (OpenAPI spec compliance)
- All inter-agent communication protocols
- All external service integrations
- All database schema changes

**Integration Tests Required For**:

- Complete user journey workflows
- Multi-agent orchestration scenarios
- Phase gate validation logic
- Evidence tier classification accuracy

**Rationale**: TDD ensures requirements are testable, reduces defects by 40-80%, and provides regression safety for continuous deployment. In AI agent systems, contract tests prevent cascading failures across agent boundaries.

### IV. AI-Human Partnership

AI agents automate routine tasks and provide intelligent recommendations. Humans retain final authority for strategic decisions, quality gates, and ethical oversight.

**AI Responsibilities**:

- Code generation, test scaffolding, documentation
- Evidence synthesis and pattern recognition
- Performance optimization recommendations
- Divergence detection and impact analysis

**Human Responsibilities (Non-Delegable)**:

- Quality gate approvals (VRC, VCD, DSP, MDP decisions)
- Strategic pivots and scope changes
- Ethical compliance verification (Safety Gates)
- Budget threshold approvals (Cost Gates)
- Architecture decisions introducing new patterns

**Rationale**: AI maximizes development velocity (40-60% faster cycles) while humans ensure strategic alignment, ethical compliance, and business judgment that AI cannot reliably provide.

### V. Systematic Traceability

Every artifact MUST maintain bidirectional traceability links: User Story → Requirements → Design → Code → Tests → Telemetry.

**Traceability Requirements**:

- PRD requirements reference user story IDs (US1, US2, etc.)
- Design wireframes reference specific requirements (FR-001, FR-002)
- Code implementation references design specifications
- Tests reference requirements and acceptance criteria
- Telemetry dashboards map to original personas and assumptions

**Cascade Impact Analysis**: When divergence is detected (Define assumptions ≠ Deploy telemetry), traceability graph determines minimum re-execution scope. Only affected Design screens and Code modules are updated, not entire phases.

**Rationale**: Traceability enables selective re-execution (days vs. weeks), impact analysis for changes, and compliance auditing. Feedback loops become actionable when telemetry can be traced back to original assumptions.

### VI. User Story Independence

Each user story MUST be independently implementable, testable, deployable, and demonstrable. User stories are prioritized (P1, P2, P3) such that implementing only P1 stories yields a viable MVP.

**Independence Requirements**:

- Each story delivers standalone user value
- Tests for story can run without dependencies on other stories
- Story can be deployed without requiring other stories
- Story can be demonstrated to users independently

**Foundational Phase Exception**: Shared infrastructure (authentication, database schema, API routing) is completed in a blocking Foundational phase before user story implementation begins.

**Rationale**: Independent stories enable parallel development by multiple teams, incremental delivery (deploy P1 stories first), and MVP flexibility (stop at any checkpoint). Aligns with spec-template.md and tasks-template.md structure.

### VII. Stack Constraint Discipline

The technology stack is frozen for MVP. No new languages, frameworks, or major dependencies may be introduced without:

1. Written justification documenting why existing stack cannot solve the problem
2. Dependency plan assessing security, maintenance, and learning curve impact
3. Explicit approval from technical leadership
4. Update to this Constitution documenting the addition

**Approved Stack (MVP)**:

- **Backend**: Node.js (LTS), TypeScript 5.x, NestJS framework
- **Frontend**: React 18.x, Next.js 14.x (App Router)
- **Database**: PostgreSQL 15.x via Supabase, Prisma ORM
- **AI/LLM**: LangGraph for orchestration, Claude 3.5 Sonnet + GPT-4 Turbo
- **Infrastructure**: Vercel (hosting), Supabase (database + auth), AWS S3 (storage)
- **Testing**: Jest (unit), Supertest (integration), Playwright (E2E)

**Rationale**: Stack discipline prevents dependency sprawl, reduces context-switching overhead, maintains consistent patterns, and ensures team expertise concentration. Optimized code within constraints beats introducing new tools.

## Technology Stack Constraints

**Approved Technologies (Do Not Modify Without Constitution Amendment)**:

**Languages & Runtimes**:

- TypeScript 5.x (strict mode enabled)
- Node.js LTS (currently v20.x)

**Frameworks & Libraries**:

- **Backend**: NestJS (dependency injection, modular architecture)
- **Frontend**: Next.js 14.x with App Router (React Server Components)
- **Database**: Prisma (type-safe ORM), PostgreSQL 15.x
- **AI Orchestration**: LangGraph (agent workflows, state management)
- **Authentication**: Supabase Auth (OAuth, JWT, RLS)

**Infrastructure & Deployment**:

- **Hosting**: Vercel (Edge Network, serverless functions)
- **Database**: Supabase (managed PostgreSQL + real-time subscriptions)
- **Storage**: AWS S3 or Supabase Storage
- **Monitoring**: Vercel Analytics, Supabase Dashboard, custom telemetry

**Testing & Quality**:

- **Unit**: Jest with ts-jest
- **Integration**: Supertest (API testing)
- **E2E**: Playwright (browser automation)
- **Type Safety**: TypeScript strict mode, ESLint, Prettier

**New Dependency Approval Process**:

1. Document limitation of existing stack
2. Evaluate 3+ alternatives with comparison matrix
3. Assess security (CVE history), maintenance (last commit, active maintainers), bundle size
4. Prototype integration with existing architecture
5. Obtain written approval from CTO or technical architect
6. Update Constitution with amendment record

## Development Workflow

**Constitution Compliance Check**: Every implementation plan MUST include a "Constitution Check" section verifying:

- Evidence tier of all critical requirements (minimum E2 for Define phase)
- Phase gate completion status (VRC/VCD/DSP/MDP as applicable)
- Test-first commitment (contract tests and integration tests identified)
- User story independence verification
- Stack constraint compliance (no unapproved dependencies)
- Traceability links documented (PRD→Design→Code)

**Plan Template Integration**: The `plan-template.md` Constitution Check section enforces this verification before Phase 0 research begins, and re-verification after Phase 1 design.

**Complexity Justification**: Any Constitution violations (e.g., introducing a 4th project when 3 is the standard, using a repository pattern when direct DB access is standard) MUST be documented in the Complexity Tracking section with:

- Why the complexity is needed
- Why simpler alternatives were insufficient
- Mitigation plan to prevent pattern spread

## Quality Gates Framework

**Gate Types & Approval Authority**:

**Quality Gates** (Product Owner approval):

- Verify deliverable completeness, accuracy, consistency
- Enforce evidence tier thresholds
- Trigger: Automated scoring below threshold (e.g., VRC <76%)

**Safety Gates** (AI Ethics Committee, non-bypassable):

- Ensure ethical compliance, bias mitigation, risk assessment
- Trigger: AI detection of potential harm, discrimination, or compliance violation
- Examples: PII exposure, discriminatory persona assumptions, regulatory non-compliance

**Cost Gates** (Financial Controller approval):

- Control budget and LLM token spend
- Trigger: Estimated costs exceed predefined thresholds
- Examples: Single feature >$5K LLM costs, monthly burn exceeds budget

**Strategic Gates** (C-Level Executive approval):

- Validate major scope changes or strategic pivots
- Trigger: Phase scope expansion >50%, technology stack changes, market pivot
- Examples: Adding new phase to 5D, changing target ICP, platform architecture redesign

**Override Protocol**: Quality/Cost gates may be overridden with written justification and risk acceptance (max 7 days retroactive documentation). Safety gates cannot be overridden under any circumstances.

## Governance

**Constitutional Authority**: This Constitution supersedes all other development practices, coding standards, and process documentation. In case of conflict, Constitution principles take precedence.

**Amendment Process**:

1. Propose amendment with rationale and impact analysis
2. Document affected templates, commands, and runtime guidance
3. Obtain approval from Product Owner (minor amendments) or CTO (major amendments)
4. Update Constitution with version increment (MAJOR.MINOR.PATCH semantic versioning)
5. Propagate changes to all dependent templates and documentation
6. Generate Sync Impact Report documenting changes

**Version Increment Rules**:

- **MAJOR**: Backward-incompatible governance changes (principle removal, gate threshold reduction)
- **MINOR**: New principles added, sections expanded, guidance materially changed
- **PATCH**: Clarifications, typo fixes, non-semantic wording improvements

**Compliance Verification**: All code reviews, PRs, and phase gate approvals MUST verify Constitution compliance. Reviewers have authority to block merges for violations.

**Continuous Improvement**: Quarterly Constitution review assesses whether principles remain effective. Metrics evaluated:

- Gate pass rates (target >90% first-time pass for Quality gates)
- Evidence tier progression (target 80% of Deploy artifacts at E3-E4)
- TDD compliance (target 100% of features have tests written first)
- Stack discipline (target zero unapproved dependencies introduced)

**Version**: 1.0.0 | **Ratified**: 2025-11-22 | **Last Amended**: 2025-11-22
