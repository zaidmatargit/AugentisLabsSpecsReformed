# Task Breakdown: Evidence Management System

## Overview
Total Tasks: 19
Estimated Effort: L (Large - foundational system used across all phases)

## Task List

### Database Layer

#### Task Group 1: Evidence Data Models
**Dependencies:** None

- [ ] 1.0 Complete evidence database layer
  - [ ] 1.1 Write 2-8 focused tests for evidence models
    - Limit to 2-8 highly focused tests maximum
    - Test critical model behaviors (tier classification, quality scoring, evidence linking)
    - Skip exhaustive coverage
  - [ ] 1.2 Create Evidence model with Prisma
    - Fields: id, project_id, assumption_text, evidence_tier, source_type, source_url, importance, category
    - Validations: evidence_tier (E0-E4), importance (critical/high/medium/low)
    - Reuse pattern from: EvidenceItem in discover/schemas.py
  - [ ] 1.3 Create EvidenceSource model
    - Fields: id, evidence_id, source_name, source_url, publication_date, credibility_score
    - Track multiple sources per evidence item
  - [ ] 1.4 Create AssumptionEvidence junction model
    - Fields: id, assumption_id, evidence_id, validation_status
    - Link evidence to specific assumptions across system
  - [ ] 1.5 Create migrations for evidence tables
    - Add indexes for: project_id, evidence_tier, category, importance
    - Foreign keys: project_id → projects
    - Add org_id for multi-tenant RLS
  - [ ] 1.6 Set up model associations
    - Evidence belongs_to Project
    - Evidence has_many EvidenceSources
    - Evidence has_many AssumptionEvidence
  - [ ] 1.7 Ensure database layer tests pass
    - Run ONLY the 2-8 tests written in 1.1
    - Verify migrations run successfully
    - Do NOT run entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 1.1 pass
- Evidence models support E0-E4 tier classification
- Evidence quality scoring logic works
- Multi-source evidence linking functional

### Core Service Layer

#### Task Group 2: Evidence Classification Service
**Dependencies:** Task Group 1

- [ ] 2.0 Complete evidence classification service
  - [ ] 2.1 Write 2-8 focused tests for classification service
    - Limit to 2-8 highly focused tests maximum
    - Test critical operations (tier assignment, quality scoring, gap analysis)
    - Skip exhaustive edge case testing
  - [ ] 2.2 Implement tier classification logic
    - Method: classifyEvidenceTier(evidenceText, sourceType): returns E0-E4
    - E0: untested ideas, E1: opinion-based, E2: qualitative insights, E3: third-party data, E4: quantitative validation
    - Use LLM analysis for automatic classification
  - [ ] 2.3 Implement evidence quality scoring
    - Method: calculateQualityScore(projectId): returns 0-100 score
    - Weight evidence by importance (critical assumptions require higher tiers)
    - Calculate tier distribution and coverage
  - [ ] 2.4 Implement gap analysis
    - Method: identifyEvidenceGaps(projectId, phase): returns gaps list
    - Identify critical assumptions with insufficient evidence (E0/E1)
    - Compare actual tier distribution vs. phase requirements
  - [ ] 2.5 Implement automatic evidence extraction
    - Method: extractEvidenceFromText(text, source): returns Evidence[]
    - Parse evidence from research reports, interviews, agent outputs
    - Auto-classify extracted evidence
  - [ ] 2.6 Ensure classification service tests pass
    - Run ONLY the 2-8 tests written in 2.1
    - Verify tier classification accuracy
    - Do NOT run entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 2.1 pass
- Evidence tier classification works (E0-E4)
- Quality scoring calculates accurately
- Gap analysis identifies low-confidence areas
- Automatic evidence extraction functional

### API Layer

#### Task Group 3: Evidence Management API
**Dependencies:** Task Groups 1, 2

- [ ] 3.0 Complete evidence management API
  - [ ] 3.1 Write 2-8 focused tests for evidence API
    - Limit to 2-8 highly focused tests maximum
    - Test critical API operations (CRUD, classification, quality score retrieval)
    - Skip exhaustive endpoint testing
  - [ ] 3.2 Create evidence controller
    - Actions: POST /evidence (create), GET /evidence/:id, PUT /evidence/:id, DELETE /evidence/:id
    - GET /projects/:id/evidence (list all evidence for project)
    - Follow pattern from: projects router
  - [ ] 3.3 Create evidence quality endpoints
    - GET /projects/:id/evidence-quality (quality score and tier distribution)
    - GET /projects/:id/evidence-gaps (identify gaps for current phase)
  - [ ] 3.4 Create evidence search and filtering
    - GET /evidence/search?tier=E2&category=market&importance=critical
    - Support filtering by tier, category, importance, source
  - [ ] 3.5 Create evidence linking endpoints
    - POST /assumptions/:id/link-evidence (link evidence to assumption)
    - GET /assumptions/:id/evidence (retrieve linked evidence)
  - [ ] 3.6 Add authorization and tenant isolation
    - Validate user can only access their org's evidence
    - Apply RLS policies through org_id context
  - [ ] 3.7 Ensure API layer tests pass
    - Run ONLY the 2-8 tests written in 3.1
    - Verify critical CRUD operations work
    - Do NOT run entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 3.1 pass
- Evidence CRUD operations functional
- Quality score and gap analysis endpoints work
- Search and filtering operational
- Multi-tenant isolation enforced

### Frontend Components

#### Task Group 4: Evidence Dashboard UI
**Dependencies:** Task Group 3

- [ ] 4.0 Complete evidence dashboard UI
  - [ ] 4.1 Write 2-8 focused tests for evidence UI
    - Limit to 2-8 highly focused tests maximum
    - Test critical UI behaviors (evidence list, tier badges, quality dashboard)
    - Skip exhaustive component testing
  - [ ] 4.2 Create evidence tier badge component
    - Display E0-E4 badges with color coding
    - E0: red, E1: orange, E2: yellow, E3: light green, E4: green
    - Show tier label and tooltip with definition
  - [ ] 4.3 Create evidence list table component
    - Columns: assumption, evidence tier, source, category, importance, actions
    - Enable sorting by tier, importance, date
    - Support inline editing and deletion
  - [ ] 4.4 Create evidence quality dashboard
    - Display overall quality score (0-100) with gauge
    - Show tier distribution donut chart (percentage at each tier)
    - Display coverage heatmap by assumption category
  - [ ] 4.5 Create evidence gap alerts component
    - Highlight critical assumptions with E0/E1 evidence
    - Show gap count and missing tier requirements
    - Provide recommendations to upgrade evidence
  - [ ] 4.6 Build evidence form component
    - Fields: assumption text, evidence tier, source URL, category, importance
    - Dropdown for tier selection (E0-E4) with descriptions
    - Multi-source input for multiple citations
  - [ ] 4.7 Apply responsive design
    - Mobile: 320px - 768px (stacked layout)
    - Tablet: 768px - 1024px (grid layout)
    - Desktop: 1024px+ (multi-column dashboard)
  - [ ] 4.8 Ensure UI tests pass
    - Run ONLY the 2-8 tests written in 4.1
    - Verify critical UI components work
    - Do NOT run entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 4.1 pass
- Evidence tier badges display correctly
- Evidence list table functional with sorting
- Quality dashboard shows score and distribution
- Gap alerts highlight low-confidence areas
- Evidence form supports CRUD operations

### Integration Layer

#### Task Group 5: Agent Integration
**Dependencies:** Task Groups 1, 2

- [ ] 5.0 Complete agent integration for evidence extraction
  - [ ] 5.1 Write 2-8 focused tests for agent integration
    - Limit to 2-8 highly focused tests maximum
    - Test critical integration points (evidence extraction from agent outputs, auto-classification)
    - Skip exhaustive integration scenarios
  - [ ] 5.2 Integrate with Research Scout Agent
    - Extract evidence from ResearchOutputs
    - Auto-classify research findings as E2/E3 based on source credibility
    - Link evidence to market assumptions
  - [ ] 5.3 Integrate with Problem Validation Agent
    - Extract evidence from interview insights (E2 tier)
    - Link pain points to problem evidence
    - Track interview sources
  - [ ] 5.4 Integrate with Opportunity Scoring Agent
    - Extract evidence from TAM/SAM/SOM calculations
    - Validate market size assumptions with evidence tiers
    - Link to opportunity metrics
  - [ ] 5.5 Create evidence aggregation service
    - Method: aggregateEvidenceFromAgents(projectId): consolidates all agent evidence
    - Deduplicate evidence from multiple sources
    - Calculate consolidated quality score
  - [ ] 5.6 Ensure integration tests pass
    - Run ONLY the 2-8 tests written in 5.1
    - Verify evidence extraction from agents works
    - Do NOT run entire test suite

**Acceptance Criteria:**
- The 2-8 tests written in 5.1 pass
- Evidence extracted from all agent outputs
- Auto-classification assigns correct tiers
- Evidence linked to assumptions properly
- Aggregation service consolidates evidence

### Testing

#### Task Group 6: Test Review & Gap Analysis
**Dependencies:** Task Groups 1-5

- [ ] 6.0 Review existing tests and fill critical gaps only
  - [ ] 6.1 Review tests from Task Groups 1-5
    - Review tests from database, classification service, API, UI, integration engineers
    - Total existing tests: approximately 10-40 tests
  - [ ] 6.2 Analyze test coverage gaps for Evidence Management feature only
    - Identify critical user workflows lacking coverage
    - Focus on end-to-end evidence tracking workflow (extract → classify → link → quality score)
    - Prioritize integration tests over unit test gaps
  - [ ] 6.3 Write up to 10 additional strategic tests maximum
    - Add maximum of 10 new tests to fill critical gaps
    - Focus on: complete evidence lifecycle, tier validation, multi-tenant isolation
    - Do NOT write comprehensive coverage
  - [ ] 6.4 Run feature-specific tests only
    - Run ONLY tests related to Evidence Management feature
    - Expected total: approximately 20-50 tests maximum
    - Verify critical evidence workflows pass

**Acceptance Criteria:**
- All Evidence Management-specific tests pass (approximately 20-50 tests total)
- Critical evidence tracking workflow covered end-to-end
- No more than 10 additional tests added
- Testing focused exclusively on Evidence Management requirements

## Execution Order

Recommended implementation sequence:
1. Database Layer (Task Group 1) - Evidence data models
2. Core Service Layer (Task Group 2) - Classification and quality scoring
3. API Layer (Task Group 3) - Evidence management endpoints
4. Frontend Components (Task Group 4) - Evidence dashboard UI
5. Integration Layer (Task Group 5) - Agent integration for auto-extraction
6. Testing (Task Group 6) - Test review and gap analysis
