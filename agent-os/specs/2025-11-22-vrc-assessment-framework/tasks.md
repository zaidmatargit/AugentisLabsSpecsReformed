# Task Breakdown: VRC Assessment Framework

## Overview
Total Tasks: 20
Estimated Effort: L (Large - comprehensive scoring system with multiple integrations)

## Task List

### Database Layer

#### Task Group 1: VRC Data Models and Migrations
**Dependencies:** None

- [ ] 1.0 Complete VRC database layer
  - [ ] 1.1 Write 2-8 focused tests for VRC models
    - Limit to 2-8 highly focused tests maximum
    - Test critical VRC model behaviors (indicator scoring, evidence tier validation, composite score calculation)
    - Skip exhaustive coverage of all methods and edge cases
  - [ ] 1.2 Create VRCAssessment model with Prisma
    - Fields: id, project_id, composite_score, assessment_date, status, phase_gate_decision
    - Validations: composite_score (0-100), status enum, required relationships
    - Reuse pattern from: Project model in core/models.py
  - [ ] 1.3 Create VRCIndicator model
    - Fields: id, assessment_id, indicator_name, dimension, score, weight, evidence_tier
    - Validations: score (0-100), evidence_tier (E0-E4), weight (0-1)
    - Support all 20 VRC indicators across 5 dimensions
  - [ ] 1.4 Create VRCEvidence model
    - Fields: id, indicator_id, evidence_item_id, source, validation_status
    - Foreign keys: links to VRCIndicator and EvidenceItem (from evidence management)
  - [ ] 1.5 Create migrations for VRC tables
    - Add indexes for: project_id, assessment_date, composite_score
    - Foreign keys: project_id → projects, evidence_item_id → evidence_items
    - Add org_id for multi-tenant RLS
  - [ ] 1.6 Set up model associations
    - VRCAssessment belongs_to Project
    - VRCAssessment has_many VRCIndicators
    - VRCIndicator has_many VRCEvidence
    - VRCEvidence belongs_to EvidenceItem
  - [ ] 1.7 Ensure database layer tests pass
    - Run ONLY the 2-8 tests written in 1.1
    - Verify migrations run successfully
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 1.1 pass
- VRC models pass validation tests
- Migrations run successfully with RLS policies
- All 20 indicators can be stored and scored

### API Layer

#### Task Group 2: VRC Assessment API Endpoints
**Dependencies:** Task Group 1

- [ ] 2.0 Complete VRC API layer
  - [ ] 2.1 Write 2-8 focused tests for VRC API endpoints
    - Limit to 2-8 highly focused tests maximum
    - Test critical API operations (create assessment, calculate score, phase gate decision)
    - Skip exhaustive testing of all endpoints and scenarios
  - [ ] 2.2 Create VRC assessment controller
    - Actions: POST /assessments (create), GET /assessments/:id (retrieve), PUT /assessments/:id/calculate (score)
    - Follow pattern from: projects router in api/routers/projects.py
  - [ ] 2.3 Implement VRC scoring service
    - calculateCompositeScore(indicators): weighted average of all 20 indicators
    - validateEvidenceTiers(indicators): ensure critical indicators have E2+ evidence
    - determinePhaseGateDecision(score): pass if score ≥76%, fail otherwise
  - [ ] 2.4 Create indicator management endpoints
    - POST /assessments/:id/indicators (add indicator score)
    - PUT /indicators/:id (update score/evidence)
    - GET /assessments/:id/indicators (list all 20 indicators)
  - [ ] 2.5 Implement evidence linking endpoints
    - POST /indicators/:id/evidence (link evidence to indicator)
    - GET /indicators/:id/evidence (retrieve evidence for indicator)
  - [ ] 2.6 Add authorization and tenant isolation
    - Validate user can only access their org's VRC assessments
    - Apply RLS policies through org_id context
  - [ ] 2.7 Ensure API layer tests pass
    - Run ONLY the 2-8 tests written in 2.1
    - Verify critical CRUD operations work
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 2.1 pass
- VRC assessment CRUD operations work
- Composite score calculation accurate (weighted average)
- Phase gate decision logic correct (≥76% = pass)
- Multi-tenant isolation enforced

### Agent/AI Layer

#### Task Group 3: VRC Scoring Agent
**Dependencies:** Task Group 2

- [ ] 3.0 Complete VRC scoring agent
  - [ ] 3.1 Write 2-8 focused tests for VRC agent
    - Limit to 2-8 highly focused tests maximum
    - Test critical agent behaviors (indicator scoring, evidence classification, recommendation generation)
    - Skip exhaustive testing of all agent states
  - [ ] 3.2 Create VRC Scoring Agent using LangGraph StateGraph
    - Reuse pattern from: discover/agent.py
    - States: parse_inputs → score_indicators → classify_evidence → calculate_composite → generate_recommendations
  - [ ] 3.3 Implement indicator scoring logic
    - Score each of 20 indicators (0-100) based on evidence quality and completeness
    - Apply default weights per dimension (problem 25%, market 25%, solution 20%, team 15%, traction 15%)
    - Support custom weights for Enterprise tier (pass as parameter)
  - [ ] 3.4 Implement evidence tier classification
    - Classify evidence as E0-E4 using LLM analysis
    - Require minimum E2 for critical indicators
    - Flag low-confidence areas (E0/E1) for validation
  - [ ] 3.5 Implement recommendation generation
    - Generate 3-5 actionable recommendations to improve VRC score
    - Prioritize recommendations by score impact
    - Link recommendations to specific indicators and evidence gaps
  - [ ] 3.6 Add structured output validation
    - Use Pydantic models for VRC scoring output
    - Validate score ranges (0-100), evidence tiers (E0-E4)
    - Target <5K tokens per VRC assessment
  - [ ] 3.7 Ensure agent tests pass
    - Run ONLY the 2-8 tests written in 3.1
    - Verify critical agent workflows execute
    - Target ≥95% agent execution success rate

**Acceptance Criteria:**
- The 2-8 tests written in 3.1 pass
- Agent scores all 20 VRC indicators accurately
- Evidence tier classification works (E0-E4)
- Composite score calculation correct
- Actionable recommendations generated

### Frontend Components

#### Task Group 4: VRC Dashboard UI
**Dependencies:** Task Group 2

- [ ] 4.0 Complete VRC dashboard UI
  - [ ] 4.1 Write 2-8 focused tests for VRC components
    - Limit to 2-8 highly focused tests maximum
    - Test critical UI behaviors (score display, indicator list rendering, phase gate status)
    - Skip exhaustive testing of all component states
  - [ ] 4.2 Create VRC score gauge component
    - Display composite VRC score (0-100) with visual gauge
    - Show pass/fail threshold indicator at 76%
    - Color coding: green (≥76%), yellow (60-75%), red (<60%)
    - Reuse pattern from: similar gauge components in design system
  - [ ] 4.3 Create VRC radar chart component
    - Visualize 5 dimensions (problem, market, solution, team, traction)
    - Plot dimension scores on radar chart
    - Use Chart.js or similar library
  - [ ] 4.4 Build indicator list component
    - Display all 20 VRC indicators in table
    - Columns: indicator name, dimension, score, evidence tier, actions
    - Enable inline editing of scores and evidence
  - [ ] 4.5 Create evidence tier distribution component
    - Show donut chart of evidence tier distribution (E0-E4)
    - Highlight percentage of indicators at each tier
    - Warn if critical indicators lack E2+ evidence
  - [ ] 4.6 Build phase gate decision card
    - Display go/no-go decision based on VRC score
    - Show decision rationale and next steps
    - Enable conditional approval workflow
  - [ ] 4.7 Apply responsive design
    - Mobile: 320px - 768px (stacked layout)
    - Tablet: 768px - 1024px (hybrid layout)
    - Desktop: 1024px+ (side-by-side layout)
  - [ ] 4.8 Ensure UI component tests pass
    - Run ONLY the 2-8 tests written in 4.1
    - Verify critical component behaviors work
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 4.1 pass
- VRC score gauge displays correctly with threshold
- Radar chart shows all 5 dimensions
- Indicator list table functional with inline editing
- Evidence tier distribution visualization works
- Phase gate decision displays correctly

### Report Generation

#### Task Group 5: VRC Report Export
**Dependencies:** Task Groups 2, 4

- [ ] 5.0 Complete VRC report generation
  - [ ] 5.1 Write 2-8 focused tests for report generation
    - Limit to 2-8 highly focused tests maximum
    - Test critical report behaviors (PDF generation, data completeness, formatting)
    - Skip exhaustive testing of all report variants
  - [ ] 5.2 Implement VRC report service
    - Generate comprehensive VRC assessment report as PDF
    - Include: composite score, indicator breakdown, evidence summary, recommendations
    - Use PDF generation library (e.g., Puppeteer for server-side rendering)
  - [ ] 5.3 Create report template
    - Header: venture name, assessment date, composite score
    - Section 1: VRC score summary with gauge visualization
    - Section 2: Radar chart showing dimension strengths
    - Section 3: Indicator list table with scores and evidence tiers
    - Section 4: Evidence tier distribution and gap analysis
    - Section 5: Recommendations for improvement
    - Section 6: Phase gate decision and rationale
  - [ ] 5.4 Add report export endpoint
    - GET /assessments/:id/report (generate and download PDF)
    - Support custom report parameters (include/exclude sections)
  - [ ] 5.5 Ensure report tests pass
    - Run ONLY the 2-8 tests written in 5.1
    - Verify PDF generation works
    - Validate report data completeness

**Acceptance Criteria:**
- The 2-8 tests written in 5.1 pass
- VRC report generates as PDF successfully
- All sections included with accurate data
- Report visualizations render correctly in PDF
- Export endpoint functional

### Testing

#### Task Group 6: Test Review & Gap Analysis
**Dependencies:** Task Groups 1-5

- [ ] 6.0 Review existing tests and fill critical gaps only
  - [ ] 6.1 Review tests from Task Groups 1-5
    - Review the 2-8 tests written by database-engineer (Task 1.1)
    - Review the 2-8 tests written by api-engineer (Task 2.1)
    - Review the 2-8 tests written by agent-developer (Task 3.1)
    - Review the 2-8 tests written by ui-designer (Task 4.1)
    - Review the 2-8 tests written by report-developer (Task 5.1)
    - Total existing tests: approximately 10-40 tests
  - [ ] 6.2 Analyze test coverage gaps for VRC Assessment feature only
    - Identify critical user workflows lacking test coverage
    - Focus on end-to-end VRC assessment workflow (create → score → gate decision → report)
    - Prioritize integration tests over unit test gaps
  - [ ] 6.3 Write up to 10 additional strategic tests maximum
    - Add maximum of 10 new tests to fill identified critical gaps
    - Focus on: complete VRC workflow, multi-tenant isolation, phase gate decision logic
    - Do NOT write comprehensive coverage for all scenarios
  - [ ] 6.4 Run feature-specific tests only
    - Run ONLY tests related to VRC Assessment feature
    - Expected total: approximately 20-50 tests maximum
    - Do NOT run the entire application test suite
    - Verify critical VRC workflows pass

**Acceptance Criteria:**
- All VRC-specific tests pass (approximately 20-50 tests total)
- Critical VRC assessment workflow covered end-to-end
- No more than 10 additional tests added when filling gaps
- Testing focused exclusively on VRC Assessment feature requirements

## Execution Order

Recommended implementation sequence:
1. Database Layer (Task Group 1) - VRC models and migrations
2. API Layer (Task Group 2) - Assessment endpoints and scoring logic
3. Agent/AI Layer (Task Group 3) - VRC scoring agent with LangGraph
4. Frontend Components (Task Group 4) - VRC dashboard UI
5. Report Generation (Task Group 5) - PDF export functionality
6. Testing (Task Group 6) - Test review and gap analysis
