# Governance.md: AugentisLabs Decision Authority & Approval Workflows

**Effective Date**: November 23, 2025  
**Last Updated**: November 23, 2025  
**Alignment**: Constitution v1.0.0

---

## Organizational Structure

### Governance Committee

**Members**:

- **CTO**: Technical architecture, stack compliance, security decisions
- **Product Lead**: Feature prioritization, user story approval, market strategy
- **Evidence Review Board Lead**: Evidence tier classification, low-evidence escalation

**Meeting Cadence**: Weekly governance sync (30 min), emergency calls as needed

**Decision Authority**:

- Constitution amendments (unanimous approval required)
- Stack changes (CTO + Product Lead joint decision)
- Phase gate overrides (depends on gate level - see matrix below)
- Evidence tier disputes (Evidence Review Board Lead final decision)

### Feature Teams

**Structure**: 3-4 feature teams of 2-3 people each, organized by product phase (Discover, Define, Design, Develop, Deploy)

**Responsibilities**:

- Implement 1-2 features end-to-end (database + API + UI + tests)
- Maintain test coverage ≥80%
- Comply with Constitution principles
- Execute within approved sprint capacity

**Decision Authority**:

- Feature-level implementation decisions (within approved specification)
- Technology decisions within approved stack (stack choice, library version, etc.)
- Code style decisions (within ruff + black formatting rules)
- Cannot override: phase gates, evidence tier requirements, Constitution principles

### Evidence Review Board

**Composition**: 1 senior technical lead + 1 product strategist

**Responsibilities**:

- Review requirements with evidence tier <E2
- Recommend validation activities to raise evidence tier
- Escalate if evidence cannot reach E2 threshold
- Monthly evidence tier trend analysis

**Decision Authority**:

- Evidence tier classification for disputed requirements
- Low-evidence escalation workflows
- Cannot override: phase gates, Constitution principles

---

## Decision Authority Matrix

### By Decision Type

| Decision Type              | Authority                        | Process                                                 | Timeline | Can Override                                |
| -------------------------- | -------------------------------- | ------------------------------------------------------- | -------- | ------------------------------------------- |
| **Constitution Amendment** | Governance Committee (unanimous) | Submit proposal → 3-day review → written approval       | 3-5 days | No (constitutional requirement)             |
| **Stack Change**           | CTO + Product Lead               | Submit + risk assessment → review → written approval    | 5-7 days | By unanimous committee (exceptional)        |
| **Feature Addition**       | PM + CTO                         | Submit feature spec + evidence tier → review → approval | 2-3 days | By Governance Committee (exception process) |
| **Feature Prioritization** | Product Lead                     | Backlog ranking + sprint allocation                     | Ongoing  | By Governance Committee (strategic pivot)   |
| **Phase Gate Override**    | Depends on gate (see below)      | Submit override justification → review → approval       | 1-2 days | By higher authority level                   |
| **Evidence Tier Dispute**  | Evidence Review Board            | Present evidence + arguments → board review → decision  | 2-3 days | Cannot override (board has final authority) |
| **Test Coverage Waiver**   | Cannot override                  | Test coverage ≥80% is mandatory                         | N/A      | No (Constitution Principle III)             |
| **Code Merge (all PRs)**   | CTO + peer reviewer              | Code review checklist → checks pass → 2-approval merge  | 1 day    | Depends on failure type                     |

### By Phase Gate

| Gate               | Threshold | Normal Override Authority  | Emergency Override Authority | Conditions                                                          |
| ------------------ | --------- | -------------------------- | ---------------------------- | ------------------------------------------------------------------- |
| **VRC** (Discover) | ≥76%      | Product Lead               | Product Lead + CTO           | Max 7 days retroactive, written justification required              |
| **VCD** (Define)   | ≥75%      | Product Lead               | Product Lead + CTO           | Max 7 days retroactive, written justification required              |
| **DSP** (Design)   | ≥80%      | Product Lead + Design Lead | CTO (emergency only)         | Max 7 days retroactive, security/compliance risk assessment         |
| **MDP** (Develop)  | ≥85%      | CTO                        | CTO + Product Lead           | Max 7 days retroactive, security scan must have ≤3 medium/low vulns |

---

## Approval Workflows

### Workflow 1: Constitution Amendment

**Trigger**: Need to change governance principle, evidence tier threshold, or phase gate requirement

**Process**:

1. Requestor drafts amendment proposal including:
   - Current principle/rule
   - Proposed change
   - Business justification
   - Risk assessment (what could go wrong)
   - Rollback plan (how to revert if needed)
   - Resource impact
2. CTO + Product Lead review draft (3 business days)
3. Governance Committee discusses (weekly sync or emergency call)
4. Both CTO + Product Lead write written approval (email acceptable)
5. Amendment added to Constitution.md with version bump
6. All team members notified via email + team chat
7. Effective date documented (same day or 7-day notice)

**Escalation**: If CTO + Product Lead disagree, escalate to company leadership (CEO/Founder)

**Timeline**: 3-5 business days (fast-track 1 day for security/compliance)

**Example**:

- Current: "Test coverage must be ≥80%"
- Proposed: "Test coverage must be ≥85% due to security-critical code"
- Justification: "3 security incidents traced to untested edge cases"
- Rollback: "If team velocity drops >30%, revert to ≥80%"

---

### Workflow 2: Stack Change Request

**Trigger**: Need new library/framework not in approved stack

**Process**:

1. Requestor submits issue with:
   - Technology name + version
   - Business case (why needed)
   - Risks (security, performance, maintenance)
   - Alternative solutions considered (why chosen)
   - Migration plan (if replacing existing tech)
   - Team skill assessment (do we have expertise)
2. CTO reviews technical feasibility (2-3 days)
3. Product Lead reviews business impact (2-3 days)
4. Joint CTO + Product Lead decision (written approval)
5. If approved, update Constitution.md approved stack list
6. Feature teams can use immediately

**Escalation**: If CTO + Product Lead disagree, Governance Committee resolves

**Timeline**: 5-7 business days

**Example**:

- Request: Add Redis for caching (not in approved stack)
- Case: "API latency >300ms P95 due to repeated DB queries"
- Risks: "New operational dependency, requires DevOps training"
- Alternative: "Prisma caching (tried, insufficient), Query optimization (insufficient)"
- Migration: "Add Redis to docker-compose.yml, integrate via BaseService layer"
- Skills: "DevOps team has Redis experience"
- Approval: CTO + Product Lead sign off on conditional approval (stack amendment 1.0.1)

---

### Workflow 3: Phase Gate Override

**Trigger**: Phase gate score below threshold, need to proceed anyway

**Process**:

1. Feature team submits override request including:
   - Current gate score (e.g., VRC 72%)
   - Reasons for gap (e.g., "Persona research incomplete, market data contradictory")
   - Remediation plan (e.g., "Conduct 5 more interviews week 2 of next phase")
   - Risk assessment (what could go wrong if we proceed)
   - Rollback plan (when to stop if things go wrong)
2. Authority for gate reviews (see matrix - depends on gate level)
3. Written approval with conditions (e.g., "Proceed to Define but reassess VRC at week 2")
4. Conditions logged in gate decision audit trail
5. Weekly check-ins on remediation progress
6. If conditions not met by deadline, automatically revert to blocking gate

**Escalation**: If override authority disagrees with request, escalate to higher authority (Governance Committee)

**Timeline**: 1-2 business days

**Conditions**:

- Retroactive approval max 7 days
- Override requires specific remediation plan + deadline
- Weekly progress check-ins mandatory
- If remediation not met, automatically revert to gate blocking

**Example**:

- VRC gate: 72% (need 76% to proceed)
- Reason: "Competitive research still in progress, market timing uncertain"
- Remediation: "Complete competitive analysis + pricing benchmarking by end of week 2 in Define phase"
- Risk: "If market analysis reveals competitor entry, may need to pivot"
- Rollback: "If competitive threat confirmed, pause Define phase, return to Discover for repositioning"
- Override Authority: Product Lead
- Approval: "Proceed to Define conditional on competitive reassessment week 2 + evidence tier E2+ for all competitive findings"

---

### Workflow 4: Evidence Tier Escalation

**Trigger**: Requirement has evidence tier <E2, blocks phase gate

**Process**:

1. Evidence Review Board identifies low-evidence requirement during phase gate review
2. Board classifies evidence gap:
   - **CRITICAL**: Blocks gate, must escalate immediately
   - **IMPORTANT**: Doesn't block gate, but flag for next phase
   - **NICE-TO-HAVE**: Informational, no action needed
3. For CRITICAL gaps, propose validation activities:
   - "Conduct 5+ customer interviews on pricing pain points"
   - "Run A/B test with 2 pricing tiers (2 weeks)"
   - "Get analyst validation (Gartner report on market sizing)"
4. Feature team executes validation or accepts risk
5. Evidence tier re-evaluated after validation
6. If evidence tier still <E2, escalate to Governance Committee for override decision

**Escalation**: If evidence cannot reach E2 threshold after 2 rounds of validation, escalate to CTO + Product Lead for strategic decision (proceed at risk or re-plan)

**Timeline**: 2-3 business days per validation cycle

**Example**:

- Requirement: "Persona 1 age 35-45, income $100K+, tech-savvy"
- Evidence: "Founder opinion + 2 interviews" (E0-E1 tier)
- Gap: Needs E2 minimum for Design phase persona-based decisions
- Validation: "Conduct 15+ interviews with ICP profile + analyze CRM data from 20 trial signups"
- Result: "Confirmed: age 35-45, income $95K-$120K range, 80% tech background"
- Updated Tier: "E2 (validated with 20+ data points)"
- Gate Impact: "Persona-based design decisions can now proceed"

---

### Workflow 5: Code Review & Merge Approval

**Trigger**: PR created, ready for review

**Process**:

1. Requestor opens PR with:
   - Feature description
   - Links to user story + task
   - Test coverage report (pytest output, ≥80% required)
   - Linting check (ruff passing)
   - New dependencies (if any, links to stack change requests)
2. CTO (or designated peer) reviews code for:
   - Constitution compliance (tests present, traceability linked, stack approved)
   - Code quality (follows patterns, documented, edge cases handled)
   - Security (no secrets in code, proper input validation, RLS compliance)
   - Performance (no N+1 queries, caching appropriate)
3. If issues found, requestor fixes + re-requests review
4. After CTO approval, second peer (feature team member) approves
5. Automated CI/CD must pass (all checks)
6. Merge to main branch

**Escalation**: If code fails CI/CD or security check, cannot merge (no waiver process)

**Timeline**: 1 business day target

**Checklist**:

- [ ] Tests present, ≥80% coverage
- [ ] Tests passing on CI/CD
- [ ] Linting passing (ruff + black)
- [ ] Linked to user story + tasks
- [ ] Traceability documented (design screen, test file)
- [ ] No new unapproved stack dependencies
- [ ] Constitution principles adhered (TDD, evidence tiers, phase gates)
- [ ] Code review comments addressed
- [ ] 2 approvals obtained

---

### Workflow 6: Feature Prioritization & Sprint Planning

**Trigger**: Quarterly planning or mid-sprint priority shift

**Process**:

1. Product Lead ranks backlog by:
   - User impact (which features unblock others)
   - Business value (revenue, retention impact)
   - Dependencies (which features are blocking)
   - Team capacity (how many features in this sprint)
2. Consultation with CTO:
   - Technical dependencies (which features require new infrastructure)
   - Technical risk (which features need spike investigation)
   - Team skill gaps (which features need training)
3. Capacity planning:
   - Average 10-12 features per sprint (46 total features / 4-5 sprints)
   - Each feature: 3-4 people, 2 weeks average
   - Parallelizable features (same phase) can run simultaneously
4. Sprint kickoff:
   - Feature team assignments
   - Acceptance criteria walkthrough
   - Risk/dependency discussion
   - Capacity confirmation

**Authority**: Product Lead (with CTO consultation on technical feasibility)

**Timeline**: 1-2 weeks per sprint planning cycle

**Escalation**: If team capacity doesn't match priority backlog, escalate to Governance Committee for strategy decision (expand team, reduce scope, extend timeline)

---

### Workflow 7: Risk Escalation (Constitution Violation)

**Trigger**: Potential Constitution violation detected (e.g., code without tests, unapproved stack used)

**Process**:

1. Detector (peer reviewer, CTO, or automated CI/CD) flags violation
2. Escalation level depends on violation severity:
   - **CRITICAL** (breaks CI/CD, security/compliance issue): Immediate escalation to CTO
   - **MAJOR** (Constitution principle violated): CTO review + decision
   - **MINOR** (style/naming issue): Feature team lead resolves
3. CTO determines:
   - Root cause (why did this happen)
   - Remediation (how to fix immediately)
   - Prevention (how to prevent in future)
4. For CRITICAL violations:
   - Code blocked until remediated
   - Post-incident review + process improvement
   - Update CI/CD checks if automated detection missed issue
5. All violations logged in governance audit trail (monthly report)

**Escalation Path**:

- CRITICAL → CTO (immediate) → Governance Committee (same-day) → Constitution amendment if process gap
- MAJOR → CTO (24 hours) → Feature team remediation
- MINOR → Feature team lead (self-remediation)

**Timeline**:

- CRITICAL: same-day decision
- MAJOR: 24-hour decision
- MINOR: 1-week remediation

**Examples**:

- **CRITICAL**: Code merged without tests (blocks all deployment)
- **MAJOR**: New library added without stack change approval (violates Constitution VI)
- **MINOR**: Function missing docstring (violates documentation standard, not Constitution)

---

## Monthly Governance Review

**Cadence**: Last Friday of every month, 1 hour

**Attendees**: CTO, Product Lead, Evidence Review Board Lead, team leads

**Agenda**:

1. Constitution compliance audit: any violations detected this month
2. Evidence tier trends: are we making decisions with increasing confidence
3. Phase gate performance: are gates scoring consistently, thresholds appropriate
4. Stack changes: any new dependencies added, any stack optimization opportunities
5. Team velocity: any blockers, capacity issues, team concerns
6. Amendment pipeline: any pending Constitution amendments, stack changes

**Output**:

- Governance audit report (distributed to team)
- Updates to governance processes if needed
- Visibility into health/risks for team

---

## Communication & Transparency

### Decision Logging

All decisions logged with:

- Date + time
- Decision type (phase gate, stack change, override, etc.)
- Decision maker(s)
- Rationale (why this decision)
- Conditions (if any)
- Impact (which teams/features affected)

### Team Communication

- **Governance Committee decisions**: Email + team chat announcement
- **Phase gate scores**: Shared with all feature teams (transparency)
- **Constitution amendments**: Team email + FAQ session if major change
- **Escalations**: Transparent communication to affected teams (what happened, why, how resolved)

### Escalation Transparency

- Escalations logged in governance audit trail
- Monthly report includes: escalations handled, root causes, prevention measures
- Team feedback loop: "What could we have done differently"

---

## Version Control

**Current Version**: 1.0.0  
**Last Updated**: November 23, 2025  
**Next Review**: Monthly (last Friday)  
**Amendment Process**: See Constitution.md
