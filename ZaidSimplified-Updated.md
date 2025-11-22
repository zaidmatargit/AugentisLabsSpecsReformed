# AugentisLabs AI Software Factory - Simplified Guide

**Version**: 2.0 (Updated with Glossary)  
**Welcome!** This document explains the entire project in plain, simple language. No jargon. No complexity.

---

## 📚 Quick Glossary (Find Definitions Below ↓)

**New to AugentisLabs?** Start here to understand the key terms. Full definitions are in Section "Glossary & Terminology" at the bottom.

| Term           | Quick Definition                                                                       |
| -------------- | -------------------------------------------------------------------------------------- |
| **TAM**        | Total Addressable Market - entire market opportunity worldwide                         |
| **SAM**        | Serviceable Addressable Market - market you can realistically reach                    |
| **SOM**        | Serviceable Obtainable Market - market you can capture in Year 1                       |
| **VRC**        | Venture Readiness Compass - gate after Discover phase (need ≥76%)                      |
| **VCD**        | Venture Competitiveness Dashboard - gate after Define phase (need ≥75%)                |
| **DSP**        | Design System Readiness Percentage - gate after Design phase (need ≥80%)               |
| **MDP**        | Milestone Delivery Readiness Percentage - gate after Develop phase (need ≥85%)         |
| **MVP**        | Minimum Viable Product - launch version with core features only                        |
| **E0-E4**      | Evidence Tiers - confidence levels from "unvalidated" (E0) to "production-proven" (E4) |
| **JTBD**       | Jobs-to-be-Done - what job does customer hire your product to do?                      |
| **Divergence** | Mismatch between Define assumptions and actual Deploy telemetry                        |
| **Cascade**    | Chain of agents re-running when divergence detected (Define→Design→Code updates)       |

---

## 🎯 The Big Picture: What Are We Building?

Imagine you have an idea for a software product. Today, you'd need to:

1. Research the market (weeks)
2. Design the product (weeks)
3. Write code (weeks)
4. Test it (weeks)
5. Deploy it (days)

**What if AI could do all of this for you?**

That's what AugentisLabs does. It's an **AI Software Factory** — a system of 26 AI agents working together to turn your idea into a fully functional, deployed software product **in 16 weeks instead of 6+ months**.

---

## 🤖 The 26 AI Agents (Exact Names from Spec)

Think of each agent as a specialist with a specific job:

### **Phase 1: DISCOVER** (4 Agents)

**Goal**: Validate your idea. Is it worth building?

1. **Ideation Agent**

   - What it does: Refines your raw 2-3 sentence idea into a clear problem statement
   - Output: Problem statement (500 words), target user profile, value proposition hypothesis
   - Evidence Tier: E0-E1 (raw hypothesis → initial exploration)

2. **Research Scout**

   - What it does: Identifies and analyzes your competitors
   - Output: 5-10 competitors with feature matrices, pricing models, market share estimates
   - Evidence Tier: E1-E2 (exploratory research + corroboration)

3. **Signal Miner**

   - What it does: Surfaces market demand signals (proof people care about this problem)
   - Output: Demand signals (Reddit threads, Twitter mentions, Google Trends spikes, news articles)
   - Evidence Tier: E1-E2 (market research + external validation)

4. **Opportunity Scorer**
   - What it does: Estimates market opportunity size and timing
   - Output: Market size (TAM/SAM/SOM), growth trajectory, timing assessment, opportunity score
   - Evidence Tier: E1-E2 (market research reports + comparable company analysis)

**Gate After Discover: VRC (Venture Readiness Compass)**

- Threshold: ≥76% to proceed to Define phase
- What's scored: 20 indicators across 4 pillars (Problem clarity, Market opportunity, Evidence quality, Founder readiness)
- If you fail: Conduct more interviews, research more competitors, get VRC ≥76%

---

### **Phase 2: DEFINE** (5 Agents)

**Goal**: Define exactly what to build and for whom.

5. **Market Research Agent**

   - What it does: Deep competitive and market intelligence analysis
   - Output: Market research report (20+ pages), demand signals, TAM validated, competitive positioning
   - Evidence Tier: E2-E3 (systematic research + analyst validation)

6. **Persona Builder**

   - What it does: Creates detailed customer profiles from interviews and research
   - Output: 3-5 personas with demographics, goals, pain points, JTBD narratives
   - Evidence Tier: E2-E3 (interview synthesis + pattern recognition)

7. **Interview Ingestion Agent**

   - What it does: Parses and extracts insights from customer interview data
   - Output: Structured interview summaries, quote extraction, pain/gain classification
   - Evidence Tier: E1-E2 (primary research extraction)

8. **Requirements Generator**

   - What it does: Creates detailed product requirements document (PRD)
   - Output: PRD with 50-100 user stories, acceptance criteria, feature prioritization (MVP vs Phase 2)
   - Evidence Tier: E2 (derived from persona research + market data)

9. **Feasibility Analyzer**
   - What it does: Assesses technical feasibility and identifies architectural needs
   - Output: Tech stack recommendations, architecture outline, risk register, effort estimates
   - Evidence Tier: E1-E2 (informed judgment from similar projects)

**Gate After Define: VCD (Venture Competitiveness Dashboard)**

- Threshold: ≥75% to proceed to Design phase
- What's scored: 10 indicators validating market research quality, feature prioritization, business model
- If you fail: Refine market research, clarify pricing strategy, target ≥75% VCD

---

### **Phase 3: DESIGN** (6 Agents)

**Goal**: Create user experience designs, system architecture, and API specifications ready for developers.

10. **UX Agent**

- What it does: Generates user interface wireframes and interaction designs
- Output: Wireframes (≥80% of user stories), user journeys, interactive prototype
- Evidence Tier: E2 (based on personas + user stories)

11. **Branding Agent**

- What it does: Develops brand identity and visual design system
- Output: Design system (Figma library): colors, typography, spacing, ≥50 components, brand guidelines
- Evidence Tier: E1-E2 (creative output informed by positioning)

12. **Journey Map Agent**

- What it does: Creates end-to-end user journey maps with emotional context
- Output: 3-5 journey maps per primary persona, emotion curves, pain/opportunity markers
- Evidence Tier: E2 (based on persona research + user stories)

13. **Architecture Agent**

- What it does: Designs system architecture and API specifications
- Output: C4 architecture diagram, OpenAPI 3.0 spec (all endpoints), database ERD, deployment diagram
- Evidence Tier: E1 (architectural judgment)

14. **Prototyper**

- What it does: Creates interactive prototype demonstrating core user flows
- Output: Interactive prototype (clickable Figma/Penpot), prototype documentation
- Evidence Tier: E2 (based on user story research + design)

15. **DSP Assembler (Design System Package)**

- What it does: Synthesizes Design phase outputs and validates design readiness
- Output: DSP document scoring 8 readiness components, gate decision (PASS/CONDITIONAL/FAIL)
- Evidence Tier: E2 (aggregation of Design phase outputs)

**Gate After Design: DSP (Design Specification Passed)**

- Threshold: ≥80% to proceed to Develop phase
- What's scored: 8 components (wireframe coverage, design system, API spec, accessibility, security design, test strategy, performance targets)
- If you fail: Complete missing wireframes, finalize API spec, design team continues iteration

---

### **Phase 4: DEVELOP** (7 Agents)

**Goal**: Generate production-ready application code with tests, security scans, and documentation.

16. **Code Generator**

- What it does: Generates backend API endpoints and frontend component scaffolding
- Output: GitHub repo with backend API (70-80% complete), frontend components, Prisma schema, test stubs, README
- Evidence Tier: E1 (generated code)

17. **Schema Generator**

- What it does: Creates database schemas and migration scripts
- Output: Prisma schema (database models), migration files, indexes, constraints, data relationships
- Evidence Tier: E1 (generated schema)

18. **Test Generator**

- What it does: Creates automated test files (unit + integration tests)
- Output: Jest test files (unit tests), Supertest integration tests, test data fixtures
- Evidence Tier: E1 (generated tests)

19. **Frontend Generator**

- What it does: Generates frontend component scaffolds and state management
- Output: Next.js components (scaffolded), React hooks, routing, API client integration stubs
- Evidence Tier: E1 (generated frontend code)

20. **Documentation Generator**

- What it does: Creates comprehensive technical and user documentation
- Output: API documentation (auto-generated), Code README, User guide, Admin guide, troubleshooting guides
- Evidence Tier: E1 (generated documentation)

21. **Security Scanner**

- What it does: Identifies and remediates security vulnerabilities
- Output: Security audit report (vulnerabilities, severity, remediation), patched code, security checklist
- Evidence Tier: E1 (security scan results)

22. **Performance & Cost Optimizer**

- What it does: Pre-deployment performance tuning and cost estimation
- Output: Optimization report (bottlenecks identified), tuned code recommendations, cost forecast (±20% accuracy)
- Evidence Tier: E1 (performance profiling data)

**Gate After Develop: MDP (Market Deployment Passed)**

- Threshold: ≥85% to proceed to Deploy phase
- What's scored: 6 components (test coverage ≥80%, code review complete, performance targets met, security vulnerabilities fixed, compliance ready, cost forecast accurate)
- If you fail: Fix failing tests, remediate security issues, improve performance, developers continue

---

### **Phase 5: DEPLOY** (4 Agents)

**Goal**: Launch to production with monitoring, governance, and continuous feedback loops.

23. **Telemetry Aggregator**

- What it does: Collects and aggregates production usage data
- Output: Telemetry dashboards (feature usage, user cohorts, retention curves, crash rates, latency)
- Evidence Tier: E4 (production telemetry = highest confidence data)

24. **Divergence Detector Agent**

- What it does: Detects mismatches between Define assumptions and actual Deploy telemetry
- Output: Divergence report (major/minor/cosmetic classifications), impact analysis, confidence scores
- Evidence Tier: E4 (telemetry comparison to E1-E2 assumptions)

25. **Define-Update Proposer**

- What it does: Generates persona and PRD update proposals based on divergences
- Output: Proposed Define v2 with diffs, update rationale (why persona age changed, why pricing doesn't fit)
- Evidence Tier: E4 (production telemetry evidence)

26. **Cascade Impact Analyzer**

- What it does: Determines selective re-execution scope (which Design screens, which Code modules need updates)
- Output: Re-execution scope (Design screens affected, Code modules affected), priority order, effort estimates
- Evidence Tier: E1 (structural impact analysis)

**Continuous Feedback Loop (No Gate)**

- Daily monitoring + weekly divergence reports
- If divergence >2 evidence tiers detected: Team reviews and approves updates → cascades to Design/Define → selective re-execution

---

## 🚀 The 5 Phases Flow (Visual)

```
┌─────────────────────────────────────────────────────────────────────┐
│ DISCOVER (1-2 weeks)                                                │
│ 4 agents validate idea; VRC score ≥76% = viable                    │
└──────────────────────┬──────────────────────────────────────────────┘
                       ↓ (VRC ≥76%)
┌─────────────────────────────────────────────────────────────────────┐
│ DEFINE (3-4 weeks)                                                  │
│ 5 agents define what to build; VCD score ≥75% = ready for design  │
└──────────────────────┬──────────────────────────────────────────────┘
                       ↓ (VCD ≥75%)
┌─────────────────────────────────────────────────────────────────────┐
│ DESIGN (3-4 weeks)                                                  │
│ 6 agents create wireframes + API specs; DSP score ≥80%             │
└──────────────────────┬──────────────────────────────────────────────┘
                       ↓ (DSP ≥80%)
┌─────────────────────────────────────────────────────────────────────┐
│ DEVELOP (4-6 weeks)                                                 │
│ 7 agents generate code + tests; MDP score ≥85%                     │
└──────────────────────┬──────────────────────────────────────────────┘
                       ↓ (MDP ≥85%)
┌─────────────────────────────────────────────────────────────────────┐
│ DEPLOY (1-2 weeks + continuous)                                     │
│ 4 agents launch + monitor; feedback loop updates Define/Design/Code │
└─────────────────────────────────────────────────────────────────────┘
```

**Total Timeline**: Idea → Live Product = **≤16 weeks** (vs 6-12 months typical)

---

## 📊 Key Concepts Explained

### What is VRC? (Venture Readiness Compass)

**VRC** scores your business idea across **20 indicators** in 4 pillars:

| Pillar                | What's Scored                                  | Example Question                                          |
| --------------------- | ---------------------------------------------- | --------------------------------------------------------- |
| **Problem** (30%)     | Problem clarity, user pain, existing solutions | Can you clearly define the pain? Do 80% of users feel it? |
| **Opportunity** (35%) | Market size (TAM), competition, unit economics | Is TAM >$100M? Only 3-5 competitors?                      |
| **Evidence** (20%)    | Interview count, source credibility            | Have you interviewed 10+ target users?                    |
| **Readiness** (15%)   | Founder expertise, resources, timeline         | Does founder have 5+ years in this space?                 |

**Gate Logic:**

- VRC ≥76% → PASS (proceed to Define)
- VRC 50-75% → CONDITIONAL (conduct more interviews, improve evidence)
- VRC <50% → FAIL (idea not viable; pivot or abandon)

### What is Evidence Tier (E0-E4)?

Every artifact in the system is classified by evidence strength:

| Tier   | Name              | Confidence | Example                                |
| ------ | ----------------- | ---------- | -------------------------------------- |
| **E0** | Unvalidated       | 0-20%      | "I think people need this"             |
| **E1** | Exploratory       | 20-40%     | 2 user interviews, Twitter mentions    |
| **E2** | Corroborated      | 40-70%     | 10+ interviews, 3 competitors analyzed |
| **E3** | Expert-Verified   | 70-85%     | Gartner report, consultant validation  |
| **E4** | Production-Proven | 85-100%    | Real telemetry from ≥1K users          |

**Why it matters**: E0 insights are risky (guesses); E4 insights are facts (measured). Gates enforce E2+ minimum.

### What Do TAM, SAM, SOM Mean?

| Term    | Definition                                                          | Example                                             |
| ------- | ------------------------------------------------------------------- | --------------------------------------------------- |
| **TAM** | Total Addressable Market - entire market opportunity worldwide      | All people who need time tracking = $12.5B globally |
| **SAM** | Serviceable Addressable Market - market you can realistically reach | Freelancers + small agencies in US + EU = $2.8B     |
| **SOM** | Serviceable Obtainable Market - market you can capture in Year 1    | Realistic Year 1 capture (1.6% of SAM) = $45M       |

**Why it matters**: If TAM is too small (<$100M), idea fails VRC. If SAM is too large, you're not focused.

### What Are the Gate Thresholds?

| Phase Gate                  | Threshold | What's Scored                                                        | Consequence If Failed                                       |
| --------------------------- | --------- | -------------------------------------------------------------------- | ----------------------------------------------------------- |
| **VRC** (Discover → Define) | ≥76%      | 20 indicators: problem, opportunity, evidence, readiness             | Re-submit with more interviews, market research             |
| **VCD** (Define → Design)   | ≥75%      | 10 indicators: market validation, product readiness, business model  | Refresh Define phase; return to agents 5-9                  |
| **DSP** (Design → Develop)  | ≥80%      | 8 components: wireframes, design system, API spec, accessibility     | Complete missing components; design team continues          |
| **MDP** (Develop → Deploy)  | ≥85%      | 6 components: test coverage, security, performance, compliance, docs | Fix failing tests, security issues, performance bottlenecks |

---

## 🎯 Real-World Example: Building Expense Tracking SaaS

**Founder's Idea**: "Mobile app for remote teams to track time and expenses"

### DISCOVER Phase (Week 1-2)

**Ideation Agent**: Refines idea to: "Context switching between Clockify (time) and Expensify (expenses) costs teams 3-5 hours/week"

**Research Scout**: Finds competitors: Clockify (18% share), Toggl (15%), Harvest (12%)

**Signal Miner**: Finds demand: Reddit post 42 upvotes, Twitter 127 mentions

**Opportunity Scorer**: Calculates TAM=$12.5B, SAM=$2.8B, SOM=$45M

**Result**: VRC Score = **78%** ✅ PASS → Proceed to Define

### DEFINE Phase (Week 3-6)

**Market Research Agent**: 25-page report with competitive analysis, pricing benchmark ($10-30/user/mo)

**Persona Builder** + **Interview Ingestion**: Creates 3 personas from 20 interviews

**Requirements Generator**: 65 user stories (time entry, expense categorization, reporting, audit trails)

**Feasibility Analyzer**: Tech stack: NestJS + PostgreSQL + React Native, 16-week effort

**Result**: VCD Score = **76%** ✅ PASS → Proceed to Design

### DESIGN Phase (Week 7-10)

**UX Agent**: 40 wireframes covering all user flows

**Branding Agent**: Colors, typography, 50+ design system components

**Journey Map Agent**: 3 journey maps highlighting pain points

**Architecture Agent**: OpenAPI spec with 15 endpoints

**Prototyper**: Clickable prototype; 5 users test; 90% would use it

**Result**: DSP Score = **82%** ✅ PASS → Proceed to Develop

### DEVELOP Phase (Week 11-18)

**Code Generator**: Backend scaffolding + 70% complete endpoints

**Schema Generator**: Prisma schema with models for Users, TimeEntries, Expenses, AuditLogs

**Test Generator**: Jest tests; initially failing (waiting for implementation)

**Security Scanner**: Passed ✅ (0 critical vulnerabilities)

**Performance & Cost Optimizer**: P95 latency 320ms (target <500ms), cost forecast $2K/month

**Result**: MDP Score = **87%** ✅ PASS → Proceed to Deploy

### DEPLOY Phase (Week 19+)

**Live**: 100 beta users sign up

**Telemetry Aggregator**: Dashboards show 5K time entries logged, 2K expenses categorized

**Divergence Detector**: "Persona is freelancer age 35, but actual users average age 38" ✅ Close, no action

**Define-Update Proposer** (triggered): "Pricing $15/month seems unfair vs $10 competitors; update pricing to $9/month"

**Cascade Impact Analyzer**: Only 3 design screens affected + 1 code module; 2-3 day fix vs 2-week redesign

**Result**: Approve update → re-design + re-code complete in 3 days → conversion improves 2% → 8%

---

## 📋 MVP vs v1.1

### MVP (Week 16, This Release) ✅

- Discover phase validation
- Define: PRD + personas + requirements
- Design: Wireframes + design system
- Develop: API scaffolding + tests + docs
- Deploy: Live with monitoring + divergence detection

### v1.1 (3-6 months later) 🔄

- Real-time collaboration (multiple team members editing simultaneously)
- Advanced ML: Better expense categorization
- Multi-region deployment (EU, APAC)
- Custom compliance templates (HIPAA, PCI-DSS)
- Advanced reporting (revenue forecasting, cohort analysis)

---

## 🏛️ 5 Core Principles (Non-Negotiable)

1. **Autonomous Phase Orchestration**: Agents work together; minimal manual handoff between phases
2. **Multi-Tenant Governance & Compliance**: Each org's data isolated; compliance policies automated; audit trails
3. **Real-Time Collaboration** (v1.1+): Live co-editing, WebSocket support, conflict-free updates
4. **Founder-to-Enterprise Versatility**: Scales from solo founder ($499/mo) to enterprise ($100K+/year)
5. **Cost Transparency & LLM Intelligence**: Every decision tracked; human fallback when LLM confidence <70%; costs capped per org

---

## ❓ FAQ

**Q1: What if my idea is too niche?**
A: If TAM <$100M, VRC fails. Recommendation: Find larger adjacent market or pivot.

**Q2: How long does each phase take?**
A: Discover 1-2 weeks, Define 3-4 weeks, Design 3-4 weeks, Develop 4-6 weeks, Deploy 1-2 weeks initial + continuous.

**Q3: Can I skip a phase?**
A: No. Gates enforce quality. You can't proceed without VRC ≥76%, VCD ≥75%, DSP ≥80%, MDP ≥85%.

**Q4: What if agents produce bad output?**
A: System retries with different prompts/models. If still poor, human escalation triggered.

**Q5: How does the feedback loop work?**
A: Deploy collects telemetry → Divergence Detector compares to Define assumptions → If divergence >2 tiers, proposes updates → Team approves → selective re-execution.

**Q6: What tech stack is required?**
A: Backend: Node.js/NestJS or Python/FastAPI; Frontend: React/Next.js; Database: PostgreSQL; Cloud: AWS/Azure/GCP; LLMs: Claude + GPT-4.

**Q7: What does it cost?**
A: Starter $499/mo (Discover only), Professional $1,999/mo (all phases, 5 projects), Enterprise custom pricing.

**Q8: Can I pivot mid-way?**
A: Yes. Restart Discover with new idea or jump back to Define. All versions preserved in history.

---

## 📖 Glossary & Terminology

Complete definitions of all AugentisLabs terminology:

### Market Sizing Terms

**TAM (Total Addressable Market)**

- Definition: The entire global market opportunity for your product category
- Example: All people who could possibly need time tracking software = $12.5 billion
- Why it matters: If TAM <$100M, market is too small; if TAM >$100B, market definition is too broad
- How it's used: VRC scoring (Opportunity pillar) checks if TAM is realistic

**SAM (Serviceable Addressable Market)**

- Definition: The subset of TAM that you can realistically reach and serve
- Example: Only freelancers + small agencies in US + EU (not global enterprises) = $2.8 billion
- Why it matters: Focuses your go-to-market strategy; shows realistic addressable opportunity
- How it's used: VCD scoring validates SAM definition is specific and actionable

**SOM (Serviceable Obtainable Market)**

- Definition: The market share you can realistically capture in Year 1
- Example: Capture 1.6% of SAM with reasonable marketing + product fit = $45 million in Year 1
- Why it matters: Used in revenue projections and business model validation
- How it's used: VCD gate ensures SOM projections are justified (not overly optimistic)

### Gate Terms

**VRC (Venture Readiness Compass)**

- Definition: Quality gate after Discover phase; scores business idea viability
- What it measures: 20 indicators across 4 pillars (Problem, Opportunity, Evidence, Readiness)
- Gate threshold: ≥76% to pass
- What happens: PASS → proceed to Define; CONDITIONAL → fix and resubmit; FAIL → idea rejected
- Why it matters: Prevents wasting time on non-viable ideas

**VCD (Venture Competitiveness Dashboard)**

- Definition: Quality gate after Define phase; validates product strategy and market fit
- What it measures: 10 indicators validating market research, personas, requirements, pricing, business model
- Gate threshold: ≥75% to pass
- What happens: PASS → proceed to Design; CONDITIONAL → refine strategy; FAIL → go back to Define
- Why it matters: Ensures strategy is well-founded before investing in design

**DSP (Design Specification Passed)**

- Definition: Quality gate after Design phase; validates design completeness
- What it measures: 8 components (wireframe coverage ≥80%, design system complete, API spec, security design, test strategy, accessibility, performance targets)
- Gate threshold: ≥80% to pass
- What happens: PASS → proceed to Develop; CONDITIONAL → complete missing specs; FAIL → design team iterates
- Why it matters: Ensures developers have everything needed to start coding without rework

**MDP (Market Deployment Passed)**

- Definition: Quality gate after Develop phase; validates code production-readiness
- What it measures: 6 components (test coverage ≥80%, code review complete, security scan passed, performance targets met, compliance ready, cost forecast accurate)
- Gate threshold: ≥85% to pass
- What happens: PASS → proceed to Deploy; CONDITIONAL → fix tests/security; FAIL → development team remediates
- Why it matters: Ensures code is safe, performant, and compliant before production launch

### Evidence Tier Terms

**E0 (Unvalidated)**

- Definition: Raw hypothesis with zero external validation
- Confidence: 0-20%
- Example: "I think remote teams need time tracking" (no interviews conducted)
- When used: Ideation phase, initial brainstorming

**E1 (Exploratory)**

- Definition: Initial exploration with 1-3 sources of evidence
- Confidence: 20-40%
- Example: 2 user interviews, Twitter mentions, Google Trends spike
- When used: Early Discover phase, preliminary market research

**E2 (Corroborated)**

- Definition: Multiple independent sources align on the claim
- Confidence: 40-70%
- Example: 10+ user interviews, 3+ competitors analyzed, analyst reports align
- When used: Define phase personas and requirements, validated market data

**E3 (Expert-Verified)**

- Definition: Industry expert or analyst has reviewed and validated
- Confidence: 70-85%
- Example: Gartner report confirms market size, consultant validates strategy
- When used: Gate scoring (gates require ≥60% E2+ evidence)

**E4 (Production-Proven)**

- Definition: Real-world data from ≥1K production users confirms
- Confidence: 85-100%
- Example: Telemetry shows 60% of power users are age 40-50, proving persona is accurate
- When used: Deploy phase divergence detection, product validation

**Evidence Tier System Rule**: Gates enforce minimum evidence quality. E0 insights alone don't pass gates; you need E2+ corroboration.

### Artifact & Workflow Terms

**JTBD (Jobs-to-be-Done)**

- Definition: The underlying job a customer is trying to accomplish when they hire your product
- Example: "I need to quickly log billable hours and categorize expenses so I can invoice my client by EOD"
- Why it matters: Personas include JTBD narratives; helps design for actual customer needs, not features
- How it's used: Requirements Generator creates user stories from JTBD definitions

**Artifact**

- Definition: Any output from an agent (document, code, design file, report)
- Examples: PRD, personas, wireframes, API specification, generated code, test files, security scan report
- Versioning: All artifacts are versioned (PRD v1, v2, v3...) with full history
- Traceability: Every artifact links to upstream requirements and downstream code/tests

**Traceability Graph**

- Definition: Links connecting PRD → Design → Code → Tests → Telemetry
- Example: "POST /api/tasks endpoint (code) ← Requirement FR-001 (PRD) ← User Story US-1 (Define) ← Persona task (Discover)"
- Why it matters: If PRD changes, you can trace what code/tests are affected
- How it's used: Cascade Impact Analyzer uses traceability to determine re-execution scope

**Divergence**

- Definition: Mismatch between Define assumptions (personas, pricing, roadmap) and actual Deploy telemetry
- Examples: "Persona said users are age 35, but telemetry shows age 38"; "Pricing assumed $15/month, but users switching to $10 competitor"
- Classification: MAJOR (>50% delta), MINOR (20-50% delta), COSMETIC (<20% delta)
- How it's handled: Divergence Detector flags → Define-Update Proposer suggests changes → Cascade Impact Analyzer determines re-execution scope

**Cascade / Selective Re-Execution**

- Definition: When divergence detected, only affected Design screens and Code modules re-run (not entire pipeline)
- Example: Persona age change affects: UX onboarding copy, age validation logic → 2-3 days fix vs re-doing entire Design phase
- Why it matters: Feedback loop provides continuous improvement without full re-work
- How it's calculated: Cascade Impact Analyzer analyzes traceability graph to find minimum re-execution scope

### Process Terms

**MVP (Minimum Viable Product)**

- Definition: Launch version with core features only, ready for beta users
- Scope: 16 weeks to reach MVP; core Discover→Define→Design→Develop→Deploy pipeline complete
- What's included: Time + expense tracking, basic reporting, 1 cloud region
- What's excluded: Real-time collab, multi-language, mobile apps, advanced analytics

**VRC Remediation**

- Definition: Process to improve VRC score if it falls below 76%
- Steps: System identifies which of 20 indicators scored low → recommends specific actions (e.g., "Conduct 5 more user interviews on persona 2") → founder conducts research → re-runs Discover → recalculates VRC
- Timeline: Usually 1-2 weeks additional

**Phase Gate / Quality Gate**

- Definition: Checkpoint between phases where system validates quality before progression
- Gates in pipeline: VRC (after Discover), VCD (after Define), DSP (after Design), MDP (after Develop)
- Decision logic: PASS (≥threshold) → proceed; CONDITIONAL (50-threshold) → fix and resubmit; FAIL (<50%) → extensive rework needed
- Override: CTO/PO can override gate decision with written justification (max 7 days)

---

## 📍 Document Map

| Need                             | Location                                             |
| -------------------------------- | ---------------------------------------------------- |
| **Full technical spec**          | `spec.md` (mandatory reading for developers)         |
| **Task breakdown & timeline**    | `tasks.md` (357 tasks, 7 layers, Sprint-by-Sprint)   |
| **Implementation plan**          | `plan.md` (architecture, tech stack, clarifications) |
| **This simplified guide**        | `ZaidSimplified.md` (non-technical overview)         |
| **Agent specification template** | `Agent-Spec-Template.md` (how to detail each agent)  |

---

## 🎓 Success Metrics

### For You (Customer)

✅ Get idea to MVP in ≤16 weeks (vs 6-12 months typical)
✅ Reduce wasted development effort by 40% (scaffolding + tests auto-generated)
✅ Full traceability (PRD → Design → Code → Telemetry)
✅ Detect strategy divergence early; respond in days (not months)

### For Us (AugentisLabs)

✅ 250K MRR by Month 12; $1M+ MRR by Month 24
✅ >80% user satisfaction
✅ >35% Day 30 retention; >60% Day 90 retention
✅ >90% gate pass rate (quality stays high)

---

## 🚀 Ready to Get Started?

1. **Read this guide** (you are here) ✅
2. **Review the full spec** (`spec.md`) for technical deep-dives
3. **Check the task breakdown** (`tasks.md`) for Sprint planning
4. **Start Discover phase**: Submit your idea, get VRC score in 4 weeks

**Questions?** See FAQ section above or reach out to the team.

---

**Created**: 2025-11-16  
**Version**: 2.1 (Updated with comprehensive glossary)  
**Status**: ✅ READY FOR USE
