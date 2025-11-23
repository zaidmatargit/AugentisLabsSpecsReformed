# Task Breakdown: Problem Validation Agent

## Overview
Total Tasks: 16
Estimated Effort: M (Medium - focused agent with structured analysis workflows)

## Task List

### Database Layer

#### Task Group 1: Problem Validation Data Models
**Dependencies:** None

- [ ] 1.0 Complete problem validation database layer
  - [ ] 1.1 Write 2-8 focused tests for problem validation models
    - Limit to 2-8 highly focused tests maximum
    - Test critical model behaviors (problem severity scoring, pain point prioritization, evidence linking)
    - Skip exhaustive coverage of all methods and edge cases
  - [ ] 1.2 Create ProblemValidation model with Prisma
    - Fields: id, project_id, analysis_date, total_problems, validated_problems_count, market_size_estimate
    - Validations: counts must be positive integers, required relationships
    - Reuse pattern from: Project model in core/models.py
  - [ ] 1.3 Create ValidatedProblem model
    - Fields: id, validation_id, problem_statement, severity_score, frequency, cost_impact, workaround_difficulty, category
    - Validations: severity_score (0-100), frequency enum, cost_impact decimal, category enum
  - [ ] 1.4 Create PainPoint model
    - Fields: id, problem_id, description, frequency_score, severity_score, priority_score, customer_segment
    - Validations: scores (0-10), priority_score = frequency × severity
  - [ ] 1.5 Create InterviewInsight model
    - Fields: id, validation_id, interview_source, quote_text, evidence_tier, problem_id
    - Foreign keys: links to ValidatedProblem and EvidenceItem (E2 classification)
  - [ ] 1.6 Create migrations for problem validation tables
    - Add indexes for: project_id, severity_score, priority_score
    - Foreign keys: project_id → projects, evidence_item_id → evidence_items
    - Add org_id for multi-tenant RLS
  - [ ] 1.7 Set up model associations
    - ProblemValidation belongs_to Project
    - ProblemValidation has_many ValidatedProblems
    - ValidatedProblem has_many PainPoints
    - ValidatedProblem has_many InterviewInsights
  - [ ] 1.8 Ensure database layer tests pass
    - Run ONLY the 2-8 tests written in 1.1
    - Verify migrations run successfully
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 1.1 pass
- Problem validation models pass validation tests
- Migrations run successfully with RLS policies
- Problem severity and priority scoring logic works

### Agent/AI Layer

#### Task Group 2: Problem Validation Agent
**Dependencies:** Task Group 1

- [ ] 2.0 Complete problem validation agent
  - [ ] 2.1 Write 2-8 focused tests for problem validation agent
    - Limit to 2-8 highly focused tests maximum
    - Test critical agent behaviors (interview parsing, pain point extraction, severity scoring)
    - Skip exhaustive testing of all agent states
  - [ ] 2.2 Create Problem Validation Agent using LangGraph StateGraph
    - Reuse pattern from: discover/agent.py
    - States: parse_interviews → extract_problems → quantify_pain_points → calculate_severity → validate_market_size → generate_report
  - [ ] 2.3 Implement interview analysis logic
    - Parse customer interview transcripts and extract key themes
    - Identify problem statements, pain points, and desired outcomes
    - Categorize problems by domain (workflow inefficiency, cost burden, compliance risk, etc.)
    - Tag interview quotes with E2 evidence tier classification
  - [ ] 2.4 Implement pain point quantification
    - Quantify pain point frequency (how often does this problem occur?)
    - Estimate pain point cost (time lost, money spent, opportunity cost)
    - Assess pain point severity on 1-10 scale
    - Calculate priority_score = frequency × severity
  - [ ] 2.5 Implement problem severity scoring
    - Calculate composite problem severity score (0-100)
    - Weight by frequency, cost impact, and workaround difficulty
    - Classify problems: critical (>75), important (50-75), minor (<50)
  - [ ] 2.6 Implement market size validation
    - Estimate number of potential customers experiencing this problem
    - Validate problem market size against TAM/SAM estimates
    - Identify problem prevalence trends (growing, stable, declining)
  - [ ] 2.7 Add structured output validation
    - Use Pydantic models for problem validation output
    - Use schema from: discover/schemas.py (EvidenceItem pattern)
    - Target <5K tokens per problem validation analysis
  - [ ] 2.8 Ensure agent tests pass
    - Run ONLY the 2-8 tests written in 2.1
    - Verify critical agent workflows execute
    - Target ≥95% agent execution success rate

**Acceptance Criteria:**
- The 2-8 tests written in 2.1 pass
- Agent parses interview transcripts successfully
- Pain points extracted and quantified accurately
- Problem severity scoring logic correct
- Market size validation functional
- Structured output validates against schema

### API Layer

#### Task Group 3: Problem Validation API
**Dependencies:** Task Groups 1, 2

- [ ] 3.0 Complete problem validation API layer
  - [ ] 3.1 Write 2-8 focused tests for API endpoints
    - Limit to 2-8 highly focused tests maximum
    - Test critical API operations (trigger validation, retrieve results, problem prioritization)
    - Skip exhaustive testing of all endpoints
  - [ ] 3.2 Create problem validation controller
    - Actions: POST /problem-validation (trigger), GET /problem-validation/:id (results)
    - Follow pattern from: projects router in api/routers/projects.py
  - [ ] 3.3 Implement agent execution endpoint
    - POST /problem-validation/execute
    - Accept interview transcripts as input
    - Trigger Problem Validation Agent asynchronously
    - Return execution status and job ID
  - [ ] 3.4 Create problem retrieval endpoints
    - GET /problem-validation/:id/problems (list validated problems)
    - GET /problems/:id (retrieve single problem with pain points)
    - Support filtering: by severity, category, customer segment
  - [ ] 3.5 Add authorization and tenant isolation
    - Validate user can only access their org's problem validations
    - Apply RLS policies through org_id context
  - [ ] 3.6 Ensure API layer tests pass
    - Run ONLY the 2-8 tests written in 3.1
    - Verify critical CRUD operations work
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 3.1 pass
- Agent execution endpoint triggers analysis successfully
- Problem retrieval endpoints return accurate data
- Filtering and sorting works correctly
- Multi-tenant isolation enforced

### Frontend Components

#### Task Group 4: Problem Validation UI
**Dependencies:** Task Group 3

- [ ] 4.0 Complete problem validation UI
  - [ ] 4.1 Write 2-8 focused tests for UI components
    - Limit to 2-8 highly focused tests maximum
    - Test critical UI behaviors (problem list rendering, severity heatmap, interview upload)
    - Skip exhaustive testing of all component states
  - [ ] 4.2 Create interview upload component
    - File upload for interview transcripts (.txt, .md formats)
    - Paste text area for direct input
    - Multiple interview batch upload support
  - [ ] 4.3 Create problem severity heatmap component
    - Visualize problems on frequency vs. impact matrix
    - Color coding by severity classification (critical/important/minor)
    - Interactive: click problem to view details
  - [ ] 4.4 Build validated problems list component
    - Display top 5-10 validated problems in table
    - Columns: problem statement, severity score, frequency, cost impact, category, evidence tier
    - Enable sorting by severity, frequency, or cost
  - [ ] 4.5 Create pain point breakdown component
    - Show pain points for selected problem
    - Display priority scores (frequency × severity)
    - Link to interview quotes as evidence
  - [ ] 4.6 Build problem validation scorecard
    - Display total problems found, validated problems, market size estimate
    - Show confidence indicators per problem
    - Highlight critical problems requiring immediate attention
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
- Interview upload component functional
- Problem severity heatmap renders correctly
- Validated problems list displays accurately
- Pain point breakdown shows prioritized list
- Scorecard displays key metrics

### Testing

#### Task Group 5: Test Review & Gap Analysis
**Dependencies:** Task Groups 1-4

- [ ] 5.0 Review existing tests and fill critical gaps only
  - [ ] 5.1 Review tests from Task Groups 1-4
    - Review the 2-8 tests written by database-engineer (Task 1.1)
    - Review the 2-8 tests written by agent-developer (Task 2.1)
    - Review the 2-8 tests written by api-engineer (Task 3.1)
    - Review the 2-8 tests written by ui-designer (Task 4.1)
    - Total existing tests: approximately 8-32 tests
  - [ ] 5.2 Analyze test coverage gaps for Problem Validation feature only
    - Identify critical user workflows lacking test coverage
    - Focus on end-to-end problem validation workflow (upload → analyze → view results)
    - Prioritize integration tests over unit test gaps
  - [ ] 5.3 Write up to 10 additional strategic tests maximum
    - Add maximum of 10 new tests to fill identified critical gaps
    - Focus on: complete validation workflow, interview parsing edge cases, multi-tenant isolation
    - Do NOT write comprehensive coverage for all scenarios
  - [ ] 5.4 Run feature-specific tests only
    - Run ONLY tests related to Problem Validation feature
    - Expected total: approximately 18-42 tests maximum
    - Do NOT run the entire application test suite
    - Verify critical workflows pass

**Acceptance Criteria:**
- All Problem Validation-specific tests pass (approximately 18-42 tests total)
- Critical problem validation workflow covered end-to-end
- No more than 10 additional tests added when filling gaps
- Testing focused exclusively on Problem Validation feature requirements

## Execution Order

Recommended implementation sequence:
1. Database Layer (Task Group 1) - Problem validation models
2. Agent/AI Layer (Task Group 2) - Problem validation agent with LangGraph
3. API Layer (Task Group 3) - Validation execution endpoints
4. Frontend Components (Task Group 4) - Problem validation UI
5. Testing (Task Group 5) - Test review and gap analysis
