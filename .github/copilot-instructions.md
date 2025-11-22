# AugentisLabs Platform - AI Agent Instructions

## Project Overview

AugentisLabs is an AI-powered Software Factory that transforms startup ideas into production-ready applications through a systematic **5D methodology** (Discover → Define → Design → Develop → Deploy). This is a product specification codebase documenting the platform architecture, not the implementation codebase.

**Key Documents**:
- `augentislabs-product-brief_v5.2_2025-11-3.md` - Executive specification (6,673 lines, strategic + technical)
- `ZaidSimplified-Updated.md` - Plain-language guide (684 lines, non-technical overview)

## Core Concepts & Terminology

### The 5D Methodology (Phase-Gated Workflow)
Each phase has specific agents, deliverables, and quality gates:

1. **DISCOVER** (1-2 weeks, 4 agents): Validate idea viability → **VRC gate ≥76%**
2. **DEFINE** (3-4 weeks, 5 agents): Define requirements & market fit → **VCD gate ≥75%**
3. **DESIGN** (3-4 weeks, 6 agents): Create UX/architecture → **DSP gate ≥80%**
4. **DEVELOP** (4-6 weeks, 7 agents): Generate production code → **MDP gate ≥85%**
5. **DEPLOY** (1-2 weeks+, 4 agents): Launch + continuous feedback loop → No gate

**Critical**: Gates are non-negotiable quality checkpoints. Lower scores trigger remediation workflows.

### Quality Gates (Always Use Exact Acronyms)
- **VRC** (Venture Readiness Compass): 20 indicators across 4 pillars (Problem, Opportunity, Evidence, Readiness)
- **VCD** (Venture Competitiveness Dashboard): 10 indicators validating market research & strategy
- **DSP** (Design Specification Passed): 8 components ensuring design completeness
- **MDP** (Market Deployment Passed): 6 components validating production readiness

### Evidence Tier System (E0-E4)
All artifacts are classified by confidence level:
- **E0** (0-20%): Unvalidated assumptions - "I think users need this"
- **E1** (20-40%): Exploratory - 2 interviews, Twitter mentions
- **E2** (40-70%): Corroborated - 10+ interviews, 3+ competitors analyzed
- **E3** (70-85%): Expert-verified - Gartner report, consultant validation
- **E4** (85-100%): Production-proven - Real telemetry from ≥1K users

**Rule**: Gates require E2+ evidence minimum. E0 insights alone don't pass.

### 26 AI Agents (Use Exact Names)
Examples: `Ideation Agent`, `Research Scout`, `Signal Miner`, `Opportunity Scorer` (Discover); `Market Research Agent`, `Persona Builder`, `Requirements Generator` (Define); `Code Generator`, `Schema Generator`, `Test Generator` (Develop); `Telemetry Aggregator`, `Divergence Detector Agent` (Deploy).

**Pattern**: Each agent has specific inputs/outputs, evidence tiers, and downstream dependencies.

## Architecture Patterns

### Multi-Tier SaaS Model
- **Starter** ($499/mo): Solo founders, VRC assessment + basic workflow
- **Professional** ($1,999/mo): Full 5D workflow, API access, 5 projects
- **Enterprise** (custom): White-label, custom agents, unlimited projects

### Technology Stack (Target Output)
- **Backend**: Node.js/NestJS or Python/FastAPI
- **Frontend**: React/Next.js components + state management
- **Database**: PostgreSQL with Prisma ORM
- **Cloud**: AWS/Azure/GCP with Vercel deployment
- **LLMs**: Claude + GPT-4 orchestration via LangGraph
- **Monitoring**: Real-time telemetry dashboards

### LangGraph Orchestration
Agents execute in **coordinated workflows** with:
- State persistence across phases
- Error recovery and retry logic
- Human-in-the-loop approval gates (quality, safety, cost, strategic)
- Traceability graph: PRD → Design → Code → Tests → Telemetry

## Key Workflows & Patterns

### Divergence Detection & Cascade Updates
Deploy phase continuously monitors for mismatches between assumptions (Define) and reality (telemetry):

1. **Divergence Detector Agent** compares E1-E2 assumptions vs E4 telemetry
2. If divergence >2 evidence tiers → **Define-Update Proposer** suggests changes
3. **Cascade Impact Analyzer** determines minimal re-execution scope (selective, not full pipeline)
4. Example: "Persona age 35 → actual 38" affects only UX onboarding copy + age validation logic (2-3 days vs weeks)

**Classification**: MAJOR (>50% delta), MINOR (20-50%), COSMETIC (<20%)

### Traceability & Artifact Versioning
All artifacts are versioned (PRD v1, v2, v3) with bidirectional links:
```
Telemetry (E4) → Test (E1) → Code Module (E1) → Design Screen (E2) → User Story (E2) → Persona (E2) → Interview (E1)
```
When PRD changes, trace affected downstream artifacts for selective re-generation.

### Gate Failure Remediation
- **VRC 50-75%**: Conduct more interviews, refine evidence (1-2 weeks)
- **VCD conditional**: Refresh market research, clarify pricing
- **DSP <80%**: Complete missing wireframes, finalize API specs
- **MDP <85%**: Fix failing tests, remediate security vulnerabilities

**Override**: CTO/PO can override with written justification (max 7 days).

## Documentation Conventions

### Market Sizing (Always TAM/SAM/SOM)
- **TAM**: Total Addressable Market (global opportunity, e.g., $12.5B)
- **SAM**: Serviceable Addressable Market (realistic reach, e.g., $2.8B)
- **SOM**: Serviceable Obtainable Market (Year 1 capture, e.g., $45M)

**VRC Gate Rule**: TAM <$100M fails opportunity scoring. TAM >$100B indicates over-broad definition.

### Agent Specification Format
When detailing agents, always include:
1. **Purpose**: Clear objective statement
2. **Inputs**: Required artifacts from upstream agents (with evidence tiers)
3. **Methodology**: Specific techniques/frameworks used
4. **Outputs**: Deliverables with format specifications
5. **Evidence Tier**: Input requirement (e.g., E1+) and output classification (e.g., E2)
6. **Quality Criteria**: Pass/fail thresholds
7. **Human-in-Loop**: Review points requiring stakeholder approval

### JTBD (Jobs-to-be-Done) Framework
Personas include JTBD narratives: "When [situation], I want to [motivation], so I can [expected outcome]"

Example: "When invoicing clients EOD, I want to quickly log billable hours and categorize expenses, so I can submit accurate invoices without manual spreadsheet work"

## Writing & Editing Guidelines

### When Updating Specifications
1. **Preserve version numbers**: Update header (e.g., v5.2 → v5.3) and date
2. **Maintain glossary consistency**: Check `ZaidSimplified-Updated.md` glossary for canonical definitions
3. **Evidence tier rigor**: Never downgrade evidence requirements (E2 → E1) without justification
4. **Gate thresholds are fixed**: VRC ≥76%, VCD ≥75%, DSP ≥80%, MDP ≥85% (never change)
5. **26 agents count is fixed**: Adding/removing agents requires major version bump + full dependency review

### Cross-Reference Pattern
When mentioning phases, always link gates: "Discover phase (VRC ≥76%)" or "Define outputs (VCD gate)"

### Acronym First Use
First mention: "Venture Readiness Compass (VRC)". Subsequent: "VRC" only.

## Common Pitfalls to Avoid

❌ **Don't** conflate this spec codebase with the platform implementation codebase  
❌ **Don't** lower gate thresholds (VRC <76%) without executive approval  
❌ **Don't** suggest E0/E1 evidence suffices for critical decisions (gates require E2+)  
❌ **Don't** propose skipping phases or gates (methodology is sequential + gated)  
❌ **Don't** reference generic "agents" - always use exact names (e.g., `Opportunity Scorer`, not "scoring agent")

✅ **Do** maintain evidence tier classifications when editing content  
✅ **Do** preserve traceability when modifying agent flows  
✅ **Do** reference both executive spec and simplified guide for context  
✅ **Do** validate TAM/SAM/SOM projections against VRC opportunity scoring rules

## Questions to Ask When Clarifying

- "Which phase does this belong to?" (Discover/Define/Design/Develop/Deploy)
- "What evidence tier is this claim?" (E0-E4)
- "Which agents produce/consume this artifact?"
- "Does this change affect gate scoring?" (VRC/VCD/DSP/MDP)
- "What's the traceability path?" (upstream/downstream dependencies)

## Success Metrics (Reference Points)

**Platform Health**: 99.9% uptime, <300ms P95 latency, >95% agent success rate  
**Business**: $250K MRR Month 12, >35% Day 30 retention, >50 NPS  
**Customer Outcomes**: >30% achieve product-market fit (vs 10% baseline), 40-60% faster time-to-market
