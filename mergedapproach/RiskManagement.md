# RiskManagement.md: Evidence Tiers, Escalation, and Divergence Detection

**Effective Date**: November 23, 2025  
**Alignment**: Constitution v1.0.0, Governance v1.0.0

---

## Evidence Tier Classification System

All product decisions MUST be classified by evidence strength using the E0-E4 system. This prevents decisions made on hunches (E0) and ensures critical requirements have market validation (E2+).

### Evidence Tier Definitions

| Tier   | Confidence | Definition                                | Examples                                                                        | Acceptable For                                                                  | Escalation Path                                                           |
| ------ | ---------- | ----------------------------------------- | ------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **E0** | 0-20%      | Untested idea, founder opinion only       | "We think founders want X" (no validation)                                      | Brainstorming only, NOT product decisions                                       | Must escalate to Evidence Review Board if appears in critical requirement |
| **E1** | 20-40%     | Initial exploration, minimal validation   | 2 founder interviews, 1 market report (surface-level)                           | Initial problem exploration, research direction                                 | Acceptable for Define phase exploration, must escalate if blocking gate   |
| **E2** | 40-70%     | Validated assumption with market research | 10+ customer interviews, 3+ competitor analysis, pricing survey with data       | MINIMUM for phase gates, MVP feature prioritization, persona attributes         | Default for gate decisions, no escalation needed                          |
| **E3** | 70-85%     | Industry validation, analyst confirmation | Gartner report, 50+ customer interviews, A/B test results, analyst white papers | Design/Architecture decisions, enterprise features, differentiation claims      | Preferred for strategic decisions, preferred for compliance features      |
| **E4** | 85-100%    | Production-proven, quantitative data      | 1K+ users, telemetry from Deploy phase, market data from competitors            | Post-launch validation, feature prioritization for Phase 2, pricing adjustments | Gold standard for decision-making, enables feature iteration              |

### Evidence Source Types

**Primary Sources** (high weight):

- Customer interviews (direct quotes, pain points, buying triggers)
- Product telemetry (feature usage, retention, conversion)
- Market data (competitor pricing, market share, analyst reports)
- Transactional data (actual revenue, churn, CAC)

**Secondary Sources** (medium weight):

- Market research reports (Gartner, IDC, SiriusXM)
- Survey data (SurveyMonkey, Typeform responses)
- Search trend data (Google Trends, Product Hunt)
- Social listening (Reddit threads, Twitter sentiment)

**Tertiary Sources** (low weight):

- Founder assumptions (internal team opinions)
- Industry best practices (what "usually works")
- Analogies to other products (LinkedIn pricing informs our pricing)

---

## Critical Requirements & E2+ Minimum

### Categories Requiring E2+ Evidence

**1. Gate Thresholds & Success Criteria** (Cannot proceed to next phase without E2+)

- VRC gate ≥76% (venture readiness indicators must be E2+)
- VCD gate ≥75% (market research quality must be E2+)
- DSP gate ≥80% (design completeness must be E2+)
- MDP gate ≥85% (production readiness must be E2+)

**2. Personas & User Segments** (Design and product decisions depend on this)

- Demographics (age, income, geography)
- Goals and pain points (JTBD narratives)
- Decision triggers and buying criteria
- Technology comfort level
- Budget constraints

**3. MVP Feature Prioritization** (What to build vs. defer)

- P1 features (must-haves for MVP)
- P2 features (nice-to-haves, post-MVP)
- Feature sequencing (which to implement first)

**4. Pricing & Business Model** (Revenue-critical)

- Pricing tier thresholds ($499 vs. $1,999 vs. Custom)
- Unit economics (LTV, CAC, payback period)
- Go-to-market strategy (who to target first)
- Willingness-to-pay by segment

**5. Competitive Differentiation** (Market positioning)

- Key features vs. competitors
- Price positioning (premium vs. discount)
- Regulatory moat or network effects
- Technical feasibility vs. competitors

**6. Technical Architecture Decisions** (Non-reversible costs)

- Choice of primary database (PostgreSQL vs. MongoDB, etc.)
- Microservices vs. monolith
- Real-time vs. batch processing
- Multi-tenant vs. single-tenant

### Categories That Can Be E1

**1. Design Exploration** (Pre-MVP wireframe direction)

- Color palette (subject to change based on user testing)
- Typography choices
- Layout patterns (high-fidelity wireframes deferred)

**2. Experimental Features** (Post-MVP, low investment)

- Analytics dashboard visualizations
- Reporting formats
- UI micro-interactions

**3. Enterprise Scope** (Out-of-scope for MVP)

- Custom compliance features (SOC 2, HIPAA)
- White-label configuration
- Enterprise integrations (Salesforce, Slack)

---

## Evidence Tier Escalation Process

### Scenario 1: Low-Evidence Critical Requirement Detected

**Trigger**: Code review identifies requirement with <E2 evidence (e.g., persona age 35-45 with only E0 "founder thinks")

**Process**:

1. **Detection**: Code reviewer flags in PR comment: "This requirement has E0 evidence, needs E2+"
2. **Escalation**: Reviewer assigns to Evidence Review Board
3. **Evidence Review Board** (within 2 days):
   - Classifies requirement as **CRITICAL** (blocks gate) or **IMPORTANT** (doesn't block, but needs validation)
   - If CRITICAL: Proposes validation activities:
     - "Conduct 15+ customer interviews on persona age assumption"
     - "Run Google Ads A/B test targeting different age groups"
     - "Analyze CRM data from trial signups to confirm actual age distribution"
   - If IMPORTANT: Documents as technical debt, plans for Define phase
4. **Feature Team** (in next sprint):
   - Either executes validation activities, OR
   - Accepts risk with written justification (escalates to Governance Committee)
5. **Re-assessment**: After validation, evidence tier re-evaluated:
   - If E2+: Proceed with confidence
   - If still <E2: Escalate to Governance Committee for strategic decision

**Timeline**: 2-3 days per escalation cycle

**Example**:

- Persona: "Solo founders, age 35-45, income $100K+"
- Evidence: "Founder thinks this is ICP" (E0)
- Board Classification: CRITICAL (persona used in all design decisions)
- Validation: "Interview 20 trial signup users on age + income"
- Result: "Confirmed age 33-48 (slightly different), income $90-$110K"
- Updated Evidence: E2 (validated with 20+ data points)

---

### Scenario 2: Gate Threshold Cannot Be Reached

**Trigger**: Phase gate evaluation shows score 72% (need 76% for VRC), remediation efforts plateau

**Process**:

1. **Detection**: Gate calculation produces CONDITIONAL decision (50-75%)
2. **Evidence Review**: Evidence Review Board analyzes which indicators scored low:
   - "Problem clarity: E1 (only 3 interviews)"
   - "Competitive research: E2 (10 competitors analyzed)"
   - "Market timing: E0 (founder opinion)"
3. **Remediation Plan**: Board proposes specific activities to raise evidence:
   - "Conduct 10+ interviews on problem pain points (weeks 1-2 Define phase)"
   - "Run market timing survey with 50 founders (week 1)"
4. **Feature Team** executes remediation OR escalates to Governance Committee
5. **Gate Re-evaluation**: If score still <76% after remediation:
   - **Option A**: Governance Committee approves override (proceed at documented risk)
   - **Option B**: Recommend idea pivot or market repositioning
   - **Option C**: Return to Discover phase for additional research

**Timeline**: 1-2 weeks per remediation cycle

**Example**:

- VRC score: 72% (CONDITIONAL)
- Low indicators: Problem clarity (E1), Market timing (E0)
- Remediation: "15 interviews on problem validation, 50-founder survey on market window"
- After remediation: "VRC score 76% (PASS)" → Proceed to Define
- Or: "VRC score 71% (still CONDITIONAL)" → Escalate override decision

---

## Divergence Detection & Cascade Impact Analysis

### Deploy Phase: Actual Telemetry vs. Define Assumptions

**Objective**: Detect when real user behavior diverges from Define-phase assumptions, trigger selective re-execution instead of full redesign.

**Mechanism**:

1. **Define Phase**: Capture all assumptions with evidence tier:

   - Persona age 35-45 (E2: 20 interviews validated)
   - Pricing $15/month acceptable to 60% (E1: pricing survey)
   - Daily usage 2 hours (E1: founder assumption)
   - Top pain point: "Time tracking accuracy" (E2: 15 interviews quoted)

2. **Deploy Phase** (Week 1-4 post-launch):

   - Collect telemetry:
     - Actual user age: Average 42.3 (range 31-52)
     - Churn at $15 price: 45% monthly churn
     - Actual daily usage: 1.2 hours average
     - Feature adoption: Time accuracy used 78% (matches pain point)

3. **Divergence Calculation**:

   - Persona age: 35-45 assumption vs. 42.3 actual = +7.3 years (NOT significant)
   - Pricing: 60% accept $15 vs. 45% actually stay = -15 percentage points (SIGNIFICANT)
   - Usage: 2 hours assumption vs. 1.2 actual = -0.8 hours (MODERATE)
   - Pain point: Matches (no divergence)

4. **Divergence Classification**:
   - **MAJOR**: Delta >2 evidence tiers (e.g., E2 assumption vs. E4 telemetry contradicts)
   - **MINOR**: Delta 1-2 evidence tiers (e.g., pricing assumption not holding)
   - **COSMETIC**: Delta <20% or cosmetic (e.g., layout preference different)

### Cascade Impact Analysis

**Trigger**: MAJOR divergence detected

**Process**:

1. **Identify Affected Requirements**:

   - "Pricing $15/month" → PRD Requirement R2.3 → User Story 2 (Define)
   - "Daily usage 2 hours" → Design screen "Dashboard - usage stats" → Code module Dashboard.tsx
   - "Support team role needed" → Code module UserManagement + RoleService

2. **Compute Re-execution Scope** (instead of full redesign):

   - **Design**: 1-2 screens affected (update pricing, usage display)
   - **Develop**: 2-3 code modules affected (pricing service, dashboard component, role management)
   - **Test**: 1-2 test suites affected (pricing, dashboard E2E tests)
   - **Estimate**: 3-5 days effort vs. 3 weeks for full redesign

3. **Update Proposal**:
   - Define v2: Update pricing assumptions, recalculate willingness-to-pay
   - Design v2: Redesign 2 screens (pricing display, usage dashboard)
   - Develop v2: Update 3 code modules + tests

### Divergence Alert Rules

| Divergence Type        | Alert Threshold                                     | Automation                    | Response                                              |
| ---------------------- | --------------------------------------------------- | ----------------------------- | ----------------------------------------------------- |
| **Persona Attribute**  | >2 evidence tier delta OR >15% numerical difference | Email to PM + Evidence Board  | Schedule Define reassessment, low urgency             |
| **Pricing Assumption** | >20% churn delta OR unexpected price sensitivity    | Immediate Slack alert + email | Urgent pricing review, may affect revenue             |
| **Feature Adoption**   | Feature used <30% of users (if E2 requirement)      | Daily telemetry report        | Investigate UX issues vs. product-market misfit       |
| **Performance**        | API latency >500ms P95 OR frontend LCP >4s          | Automated alert to DevOps     | Investigate technical issues, prioritize optimization |
| **Crash Rate**         | >1% of sessions crash OR critical error >10% users  | Immediate escalation          | Hotfix deployment, production incident                |

---

## Risk Escalation Paths

### Path 1: Evidence Tier Violation → Evidence Review Board

**Trigger**: Requirement has <E2 evidence for critical decision

**Escalation Path**:

```
Code Reviewer detects E0 evidence
    ↓
Flag in PR (cannot merge without E2+)
    ↓
Evidence Review Board (24-48 hours)
    ↓
CRITICAL → Propose validation activities
    ↓
Feature team executes validation OR escalates
    ↓
If cannot reach E2 → Governance Committee override
```

**Timeline**: 2-5 days per cycle

---

### Path 2: Gate Threshold Not Reached → Governance Committee

**Trigger**: Phase gate score below threshold (e.g., VRC 72% need 76%)

**Escalation Path**:

```
Gate calculation produces CONDITIONAL (<76%)
    ↓
Evidence Review Board analyzes low indicators
    ↓
Proposes remediation activities (1-2 weeks)
    ↓
Feature team executes OR escalates
    ↓
If still <threshold → Governance Committee
    ↓
Override decision (proceed at risk OR replan)
```

**Timeline**: 1-2 weeks per remediation cycle, then 1-2 days for override decision

---

### Path 3: MAJOR Divergence Detected → Product Lead

**Trigger**: Production telemetry contradicts Define assumption (>2 evidence tiers)

**Escalation Path**:

```
Deploy phase divergence detection algorithm runs
    ↓
MAJOR divergence flagged (e.g., pricing <50% retention vs. 60% assumption)
    ↓
Cascade Impact Analyzer identifies affected components
    ↓
Product Lead reviews divergence + impact
    ↓
Update proposal: Define v2 + Design v2 + Develop v2
    ↓
Governance Committee approves selective re-execution scope
    ↓
Feature team executes re-execution (3-5 days vs. 3 weeks full redesign)
```

**Timeline**: 1-2 days alert → decision, then 3-5 days execution

---

## Monthly Evidence Tier Audit

**Cadence**: Last Friday of every month (Governance Review)

**Process**:

1. **Extract Evidence Metrics**:

   - Total requirements: X (across all phases)
   - By evidence tier: E0 (%), E1 (%), E2 (%), E3 (%), E4 (%)
   - Critical requirements with <E2: (red flag if >5%)
   - Escalations handled: (trends)

2. **Trend Analysis**:

   - Are we converging toward E2+? (desired trend)
   - Are low-evidence requirements being validated or ignored?
   - Do escalations follow defined paths or ad-hoc?

3. **Adjustments**:

   - If >10% critical requirements <E2 → lower gate threshold? (vice versa for <5%)
   - If Evidence Board overloaded → increase team capacity?
   - If divergence alerts noisy → adjust thresholds?

4. **Team Communication**:
   - Share metrics with all feature teams
   - Celebrate converging toward E2+ (building confidence)
   - Highlight risks from low-evidence decisions

---

## Risk Management Checklist

**Pre-Phase-Gate**:

- [ ] All critical requirements tagged with evidence tier (E0-E4)
- [ ] Critical requirements have E2+ evidence (or escalation plan)
- [ ] Evidence sources documented (links to interviews, reports, data)
- [ ] Low-evidence items reviewed by Evidence Review Board
- [ ] Gate threshold appropriate for phase (VRC ≥76%, VCD ≥75%, DSP ≥80%, MDP ≥85%)

**During Phase**:

- [ ] Team documents evidence as they learn (don't wait for gate evaluation)
- [ ] New evidence discovered → update evidence tier immediately
- [ ] Conflicting evidence → escalate to Evidence Review Board
- [ ] Gate score tracking (are we on track to pass threshold?)

**Post-Phase (Deploy)**:

- [ ] Telemetry collection configured for all Define assumptions
- [ ] Divergence detection algorithm active + tested
- [ ] Alert thresholds configured (MAJOR >2 tiers, etc.)
- [ ] Cascade analyzer tested on hypothetical divergences
- [ ] Re-execution process (Define v2 → Design v2 → Develop v2) designed + staffed

---

## Version Control

**Current Version**: 1.0.0  
**Last Updated**: November 23, 2025  
**Next Review**: Monthly (Governance sync)  
**Amendment Process**: See Constitution.md
