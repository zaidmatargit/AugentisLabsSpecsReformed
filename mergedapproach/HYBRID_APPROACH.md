# HYBRID_APPROACH.md: How We Merged Speckit + Agent-OS

**Created**: November 23, 2025  
**Purpose**: Explain the hybrid methodology, what each approach contributed, and how to use this merged framework  
**Status**: Foundation for all AugentisLabs development

---

## Executive Summary

AugentisLabs merges two planning approaches:

- **Speckit** (governance-first): Comprehensive Constitution with 6 immutable principles, evidence tier classification (E0-E4), phase gates (VRC/VCD/DSP/MDP), systematic traceability, and governance oversight. Provides rigor but sequential delivery.

- **Agent-OS** (feature-driven): Feature-centric modularity with 46 independent features, automated spec generation, reusable component patterns (8 core patterns), and parallel team execution. Provides speed but less governance.

**Hybrid Result**: Speckit's governance rigor + Agent-OS's feature parallelization = **Faster, governance-compliant, traceable delivery**.

---

## What Speckit Contributed

### 1. Constitution: 6 Immutable Governance Principles

**Located**: `Constitution.md`

**Principles**:

1. **Evidence Tier Classification (E0-E4)**: All critical decisions require E2+ market validation
2. **Phase Gates (VRC/VCD/DSP/MDP)**: Non-bypassing quality gates with hard thresholds
3. **Test-First Discipline (TDD)**: Failing tests written first (RED→GREEN→REFACTOR)
4. **Systematic Traceability**: User story → requirement → design → code → test → telemetry
5. **User Story Independence**: Each story independently deployable (MVP checkpoints)
6. **Stack Constraint**: Frozen technology stack (no unapproved changes)

**Why We Kept It**:

- Prevents bad decisions (low-evidence requirements blocked)
- Protects from scope creep (Constitution amendment process)
- Enables systematic risk management (evidence escalation paths)
- Creates accountability (governance audit trail)

---

### 2. Phase Gates: VRC/VCD/DSP/MDP with Hard Thresholds

**Located**: `Constitution.md` (Principle II), `Plan.md` (5D phases), `MasterTasks.md` (gate features)

**Gates**:

- **VRC** (Discover → Define): ≥76% venture readiness (20 indicators)
- **VCD** (Define → Design): ≥75% definition quality (10 indicators)
- **DSP** (Design → Develop): ≥80% design completeness (8 components)
- **MDP** (Develop → Deploy): ≥85% production readiness (6 components)

**Why We Kept It**:

- Prevents bad ideas from consuming engineering time (VRC gate)
- Validates market strategy before design (VCD gate)
- Ensures design completeness before code (DSP gate)
- Blocks unsafe deployments (MDP gate)
- Gate decisions logged for compliance + retrospectives

---

### 3. User Stories as Release Checkpoints

**Located**: `Plan.md` (6 user stories as releases)

**Stories**:

1. **Discover MVP**: Submit idea → receive VRC score (go/no-go decision)
2. **Define MVP**: Upload research → receive personas + PRD
3. **Design MVP**: Receive wireframes + architecture
4. **Develop MVP**: Code generation with TDD tests
5. **Deploy MVP**: Production with monitoring + divergence detection
6. **Governance MVP**: Audit trails, cost dashboards, approval workflows

**Why We Kept It**:

- Enables independent feature team deployment (Story 1 independent from Story 2)
- Creates measurable release checkpoints (4 MVP releases possible)
- Allows founder feedback between milestones
- Reduces risk (deploy Discover → validate founder feedback → proceed to Define)

---

### 4. Evidence Tier System (E0-E4)

**Located**: `RiskManagement.md` (full classification system)

**Tiers**:

- **E0** (0-20%): Founder opinion, no validation
- **E1** (20-40%): Initial exploration, 2-3 interviews
- **E2** (40-70%): Validated assumption, 10+ interviews + market research (MINIMUM for gates)
- **E3** (70-85%): Industry validation, analyst confirmation
- **E4** (85-100%): Production-proven with 1K+ user telemetry

**Why We Kept It**:

- Prevents decisions on hunches (E0 requirements escalated)
- Systematizes confidence levels (E2+ requirement for all critical decisions)
- Enables divergence detection (compare E2 assumption vs. E4 telemetry)
- Triggers re-execution scope analysis (Cascade Impact Analyzer)

---

### 5. Governance Structure & Approval Workflows

**Located**: `Governance.md` (decision authority matrix, approval workflows)

**Key Structure**:

- **Governance Committee**: CTO + Product Lead + Evidence Review Board (Constitution amendments, stack changes, gate overrides)
- **Feature Teams**: 3-4 people each, implement 1-2 features end-to-end
- **Evidence Review Board**: Validates low-evidence requirements, triggers escalation

**Why We Kept It**:

- Maintains control over critical decisions (no autonomous AI decisions)
- Escalates risks consistently (defined paths, not ad-hoc)
- Documents decision rationale (audit trail for compliance)
- Enables distributed authority (CTO decides tech, PM decides features)

---

## What Agent-OS Contributed

### 1. 46 Features Organized by Phase

**Located**: `Plan.md` (phase breakdown), `MasterTasks.md` (feature list)

**Organization**:

- **Phase 1**: Discover (9 features)
- **Phase 2**: Define (10 features)
- **Phase 3**: Design (8 features)
- **Phase 4**: Develop (10 features)
- **Phase 5**: Deploy (9 features)

**Why We Kept It**:

- Enables feature-team parallelization (8-10 teams working simultaneously)
- Reduces single-team bottleneck (no centralized "architecture team")
- Creates clear ownership (each feature team owns DB + API + UI + tests)
- Allows independent testing (each feature has isolated test suite)

---

### 2. Reusable Component Patterns (8 Core Patterns)

**Located**: `components/` folder (to be detailed)

**Patterns**:

1. **LangGraph Agent Pattern**: StateGraph orchestration (used in all 26 agents)
2. **Evidence Tier Schema**: E0-E4 classification (used in 15 validation features)
3. **Database Entity Pattern**: Multi-tenant isolation (used in 46 features, org_id + RLS)
4. **API Service Pattern**: CRUD operations with dependency injection (used in 41 features)
5. **Frontend Component Pattern**: React hooks + state management (used in 39 UI features)
6. **Testing Pattern**: TDD RED→GREEN→REFACTOR (used in 237+ tasks)
7. **API Contract Pattern**: OpenAPI 3.0 validation (used in 5 contracts)
8. **Deployment Pattern**: Feature flags + canary release (used in all Deploy features)

**Why We Kept It**:

- Reduces duplicated code (pattern reuse across 46 features)
- Accelerates feature implementation (teams copy-paste patterns)
- Ensures consistency (all agents follow same LangGraph structure)
- Simplifies onboarding (new team member learns 8 patterns, then applies everywhere)

---

### 3. Automated Spec & Task Generation (Template-Driven)

**Located**: `MasterTasks.md` (task template pattern)

**Template Per Feature**:

- Database layer: 2-3 tasks (model, migration, seeding)
- API layer: 2-3 tasks (endpoints, validation, error handling)
- Agent/Logic: 2-3 tasks (LangGraph agent, business logic, Langfuse tracing)
- Frontend: 2-3 tasks (React components, hooks, responsive design)
- Testing: 2-3 tasks (unit, integration, contract tests)
- Review: 1 task (code review + merge)

**Pattern Benefits**:

- Every feature has 10-15 tasks (consistent scope)
- New features copy template (reduces planning time)
- Task automation possible (code generators from templates)
- Velocity predictable (X features × 15 tasks = effort)

**Why We Kept It**:

- Reduces manual task definition (templates, not one-off specs)
- Enables parallelization (all 46 features on same pattern)
- Speeds up onboarding (new team member: "this feature is like feature 5, copy-paste tasks")
- Supports agile (change 1 template = update all features)

---

### 4. Feature-Driven Team Organization

**Located**: `Plan.md` (Weeks 5-16 resource capacity)

**Team Structure**:

- **Phase Teams**: 12-15 people split into 3-4 teams of 3-4 people each
- **Feature Ownership**: Each team owns 1-2 features end-to-end
- **Cross-functional**: Each team has frontend + backend + QA
- **Parallelization**: All features in a phase executed in parallel

**Example (Weeks 5-8, Discover Phase)**:

- Team 1: Features 1-3 (VRC Framework, Problem Validation, Competitive Research)
- Team 2: Features 4-6 (Opportunity Scoring, Research Scout, Evidence Management)
- Team 3: Features 7-9 (Custom Indicators, Report Generation, Phase Gate System)
- All teams run in parallel (12-15 person-days per team per week)

**Why We Kept It**:

- No bottleneck (backend engineers don't wait for design, frontend for APIs)
- Feature ownership = accountability (team responsible for quality)
- Parallel delivery = faster timeline (4-5 sprints vs. 8+ sequential sprints)
- Team autonomy = morale (teams make implementation decisions)

---

## How the Hybrid Works Together

### Governance Guards Feature Parallelization

**Problem**: Agent-OS parallelizes but could allow risky decisions (no governance gates)

**Solution**: Speckit's gates act as release coordination points

**Example**:

- Weeks 5-8: All 9 Discover features implemented in parallel (by 3 teams)
- Week 8 end: All features meet Definition of Done (tests passing, coverage ≥80%)
- Week 8: VRC gate evaluation (Phase gate system feature tests VRC gate)
- **Week 8 decision**: If VRC ≥76%, release all 9 Discover features; if <76%, remediate
- Week 9+: Define features (10 features) start in parallel, Discover features in production

**Result**: Feature parallelization + governance risk control

---

### Evidence Tiers Enable Selective Re-execution

**Problem**: Speckit traceability is theoretical without execution data; Agent-OS doesn't track evidence

**Solution**: Evidence tiers + Deploy phase divergence detection triggers selective re-execution

**Example**:

1. **Define Phase**: Persona age 35-45 (E2 evidence from 20 interviews)
2. **Design Phase**: Design all screens assuming age 35-45 persona
3. **Deploy Phase**: Telemetry shows actual user age 42 (E4 evidence from 1K users)
4. **Divergence**: 42 vs. 35 = +7 years (NOT significant, cosmetic divergence)
5. **But also**: Churn at $15/mo pricing 45% (vs. 60% assumption) = SIGNIFICANT DIVERGENCE
6. **Cascade Analyzer**: Pricing assumption → Define (PRD) → Design (pricing display screens) → Code (pricing service) = 2-3 code modules affected
7. **Re-execution**: Update Define v2 (pricing assumption) → Design v2 (2 screens) → Code v2 (3 modules) = 3-5 days vs. 3 weeks full redesign

**Result**: Evidence tiers enable intelligent, scoped re-execution (not reactionary full redesigns)

---

### Feature Teams + Constitution Amendments

**Problem**: Agent-OS distributed teams could make conflicting decisions; Speckit centralized governance is slow

**Solution**: Constitution defines non-negotiable principles; teams have autonomy within principles

**Example**:

- **Constitution says**: Stack is frozen (Python 3.11, FastAPI, SQLAlchemy, ruff)
- **Feature Team 3 asks**: "Can we add Redis for caching?"
- **Stack change process** (5-7 days):
  1. Team submits: business case (why), risks, alternatives considered, migration plan
  2. CTO reviews: technical feasibility (2 days)
  3. Product Lead reviews: business impact (2 days)
  4. Joint approval: written decision (Constitution amendment 1.0.1)
  5. Feature teams can use immediately (or revert if problems)
- **Meanwhile**: Feature Teams 1-2 continue work without waiting (no bottleneck)

**Result**: Distributed autonomy + centralized governance (best of both)

---

## How to Use This Framework

### For New Team Members

**Read in This Order**:

1. `HYBRID_APPROACH.md` (this file) - understand the philosophy
2. `Constitution.md` - learn 6 non-negotiable principles
3. `Governance.md` - understand how decisions are made
4. `Plan.md` - see the 16-week timeline + 5D phases
5. `RiskManagement.md` - learn evidence tier escalation paths
6. `MasterTasks.md` - pick a feature, follow the task template
7. `components/` - copy reusable patterns for your feature

**Timeline**: ~2 hours to understand, then start feature work

---

### For Governance Committee

**Responsibilities**:

- Review Constitution amendments (CTO + Product Lead joint approval)
- Oversee phase gates (validate gate evaluation, approve conditional passes/overrides)
- Address escalations (evidence tier disputes, low-evidence requirements)
- Monthly governance review (trends in evidence, gate effectiveness, team blockers)

**Meetings**:

- Weekly: 30-min governance sync (gate status, escalations, decisions)
- Monthly: 1-hour governance review (audit trail, metrics, process improvements)
- Emergency: Same-day decision for Constitution violations

---

### For Feature Teams

**Workflow** (per feature):

1. Pick a feature from `MasterTasks.md` (e.g., "Discover Feature 1: VRC Assessment Framework")
2. Copy task template (10-15 tasks: DB, API, Logic, Frontend, Testing, Review)
3. Complete tasks in order (DB first → API → Frontend → Testing → Review)
4. Definition of Done: tests ≥80%, ruff passing, code reviewed, merged
5. Gate evaluation: feature contributes to VRC/VCD/DSP/MDP gate score
6. Deploy: behind feature flag, release at phase gate point

---

### For Product Management

**Prioritization**:

1. **Phase Sequencing**: Discover (Phase 2) → Define (Phase 3) → Design (Phase 4) → Develop (Phase 5) → Deploy (Phase 6)
2. **Feature Ordering Within Phase**: Prioritize by user value, then by dependencies
3. **Release Coordination**: Phase gates determine release points (all features in phase must pass gate)
4. **User Story Mapping**: Feature 1-9 → User Story 1 (Discover), Features 10-19 → User Story 2 (Define), etc.

---

## Key Decisions Made in Hybrid

### Decision 1: Evidence Tiers Over "Team Opinion"

**Alternative**: Trust team estimates without evidence requirements  
**Why Hybrid Chose This**: Prevents waste on non-viable ideas (VRC gate is e.g. )

### Decision 2: Phase Gates Over Continuous Delivery

**Alternative**: Deploy continuously (no gates, just CI/CD)  
**Why Hybrid Chose This**: Prevents non-viable ideas from reaching production (gate thresholds enforce market validation)

### Decision 3: Feature Teams Over Layer Teams

**Alternative**: All backend engineers sync separately from frontend (traditional)  
**Why Hybrid Chose This**: Feature ownership + accountability; no bottlenecks waiting for other layer to finish

### Decision 4: User Stories as Release Checkpoints

**Alternative**: Deploy all features at end (16-week big bang)  
**Why Hybrid Chose This**: Enables founder feedback after each phase; reduces risk

### Decision 5: Reusable Patterns Over One-Off Specs

**Alternative**: Design each feature independently  
**Why Hybrid Chose This**: 46 features same pattern = predictable velocity + faster onboarding

---

## Potential Risks & Mitigations

| Risk                                         | Impact                                   | Mitigation                                                    | Owner        |
| -------------------------------------------- | ---------------------------------------- | ------------------------------------------------------------- | ------------ |
| **Gate thresholds too strict**               | Slow phase progression, team frustration | Monthly governance review, adjust if blocked 3x               | CTO + PM     |
| **Feature teams have conflicting decisions** | Inconsistent code quality, rework        | Constitution guards principles, code review enforces patterns | CTO          |
| **Evidence escalations overwhelm board**     | Delays in decision-making                | Add Evidence Review Board capacity if >10 escalations/sprint  | PM           |
| **Distributed teams don't communicate**      | Duplicated work, gaps in features        | Weekly sync, shared traceability map, clear ownership         | Team Leads   |
| **LLM API downtime during agent execution**  | Feature teams blocked on AI models       | Fallback to Claude, queue requests, retry logic               | Backend Lead |

---

## Success Metrics for Hybrid Approach

| Metric                        | Target                                        | How We Measure                   |
| ----------------------------- | --------------------------------------------- | -------------------------------- |
| **Timeline**                  | 16 weeks MVP                                  | Project completion date vs. plan |
| **Feature Parallelization**   | 8-10 features in parallel (per phase)         | Team utilization metrics         |
| **Evidence Tier Convergence** | 60% of critical requirements E2+ by week 8    | Evidence audit report            |
| **Gate Pass Rate**            | 80%+ first-time passes                        | Gate evaluation metrics by phase |
| **Code Quality**              | ≥80% test coverage across all features        | Pytest coverage reports          |
| **Governance Effectiveness**  | <2 Constitution violations per sprint         | Escalation audit trail           |
| **Team Velocity**             | 46 features ÷ 4 sprints = ~11 features/sprint | Sprint capacity tracking         |
| **Founder Satisfaction**      | >8/10 NPS at each phase gate                  | Post-phase founder survey        |

---

## Version Control

**Hybrid Approach Version**: 1.0.0  
**Created**: November 23, 2025  
**Status**: Foundation for all development  
**Amendment Process**: See Constitution.md (amendments tracked in Constitution v history)

---

## Questions?

- **Constitution questions** → See Constitution.md (6 principles explained)
- **How decisions are made** → See Governance.md (approval workflows)
- **What we're building** → See Plan.md (16-week timeline)
- **Risk & evidence** → See RiskManagement.md (escalation paths)
- **What to build next** → See MasterTasks.md (pick a feature + follow template)
- **Code patterns** → See components/ folder (8 reusable patterns with examples)
