# Task Breakdown: Competitive Research Agent

## Overview
Total Tasks: 17
Estimated Effort: M (Medium - web search integration with structured analysis)

## Task List

### Database Layer

#### Task Group 1: Competitive Research Data Models
**Dependencies:** None

- [ ] 1.0 Complete competitive research database layer
  - [ ] 1.1 Write 2-8 focused tests for competitive research models
    - Limit to 2-8 highly focused tests maximum
    - Test critical model behaviors (competitor profiling, feature comparison, positioning storage)
    - Skip exhaustive coverage of all methods and edge cases
  - [ ] 1.2 Create CompetitiveResearch model with Prisma
    - Fields: id, project_id, research_date, total_competitors, search_queries_used, tier_limit
    - Validations: tier_limit (10 for Starter, 50 for Professional, unlimited for Enterprise)
    - Reuse pattern from: ResearchOutputs in discover/schemas.py
  - [ ] 1.3 Create CompetitorProfile model
    - Fields: id, research_id, name, website, description, funding, headquarters, competitor_type, logo_url
    - Validations: competitor_type enum (direct, indirect, substitute), website URL format
    - Reuse pattern from: CompanyProfile in discover/schemas.py
  - [ ] 1.4 Create CompetitorFeature model
    - Fields: id, competitor_id, feature_name, feature_category, has_feature, feature_description
    - Support feature comparison matrix generation
  - [ ] 1.5 Create PositioningRecommendation model
    - Fields: id, research_id, recommendation_text, differentiation_angle, evidence_summary, priority
    - Validations: differentiation_angle enum (technology, pricing, target_segment, etc.)
  - [ ] 1.6 Create migrations for competitive research tables
    - Add indexes for: project_id, research_date, competitor_type
    - Foreign keys: project_id → projects
    - Add org_id for multi-tenant RLS
  - [ ] 1.7 Set up model associations
    - CompetitiveResearch belongs_to Project
    - CompetitiveResearch has_many CompetitorProfiles
    - CompetitorProfile has_many CompetitorFeatures
    - CompetitiveResearch has_many PositioningRecommendations
  - [ ] 1.8 Ensure database layer tests pass
    - Run ONLY the 2-8 tests written in 1.1
    - Verify migrations run successfully
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 1.1 pass
- Competitive research models pass validation tests
- Migrations run successfully with RLS policies
- Competitor profile and feature comparison data structure works

### Agent/AI Layer

#### Task Group 2: Competitive Research Agent
**Dependencies:** Task Group 1

- [ ] 2.0 Complete competitive research agent
  - [ ] 2.1 Write 2-8 focused tests for competitive research agent
    - Limit to 2-8 highly focused tests maximum
    - Test critical agent behaviors (competitor discovery, feature extraction, positioning generation)
    - Skip exhaustive testing of all agent states
  - [ ] 2.2 Create Competitive Research Agent using LangGraph StateGraph
    - Reuse pattern from: discover/agent.py
    - States: search_competitors → extract_profiles → map_features → analyze_gaps → generate_positioning → cache_results
  - [ ] 2.3 Implement web search integration
    - Integrate with search APIs (e.g., Tavily, SerpAPI) for competitor discovery
    - Implement tiered search limits (10/50/unlimited by tier: Starter/Professional/Enterprise)
    - Cache search results to minimize API costs (store in Redis with 30-day TTL)
    - Extract structured data from search results
  - [ ] 2.4 Implement competitor discovery logic
    - Identify top 10-15 competitors through AI-powered web search
    - Extract competitor profiles (name, website, funding, headquarters, description)
    - Categorize competitors by type (direct, indirect, substitute solutions)
    - Validate competitor relevance using LLM analysis
  - [ ] 2.5 Implement feature mapping
    - Map competitor features using structured data extraction
    - Extract feature lists from competitor websites and public sources
    - Normalize feature names for comparison
    - Build feature comparison matrix
  - [ ] 2.6 Implement feature gap analysis
    - Compare venture's proposed features against competitor offerings
    - Identify feature gaps where competitors are ahead
    - Highlight unique features not offered by competitors
    - Quantify feature coverage percentage vs. market leaders
  - [ ] 2.7 Implement positioning recommendation generation
    - Generate 3-5 positioning strategy recommendations
    - Identify white space opportunities in the market
    - Suggest differentiation angles (technology, pricing, target segment, etc.)
    - Validate positioning against evidence from market research
  - [ ] 2.8 Add structured output validation
    - Use Pydantic models for competitive research output
    - Reuse pattern from: ResearchOutputs, CompanyProfile in discover/schemas.py
    - Target <5K tokens per competitive analysis
  - [ ] 2.9 Ensure agent tests pass
    - Run ONLY the 2-8 tests written in 2.1
    - Verify critical agent workflows execute
    - Target ≥95% agent execution success rate

**Acceptance Criteria:**
- The 2-8 tests written in 2.1 pass
- Agent discovers top 10-15 competitors successfully
- Competitor profiles extracted with key data
- Feature comparison matrix generated accurately
- Feature gap analysis identifies gaps and unique features
- Positioning recommendations actionable (3-5 recommendations)
- Search query limits enforced by tier

### API Layer

#### Task Group 3: Competitive Research API
**Dependencies:** Task Groups 1, 2

- [ ] 3.0 Complete competitive research API layer
  - [ ] 3.1 Write 2-8 focused tests for API endpoints
    - Limit to 2-8 highly focused tests maximum
    - Test critical API operations (trigger research, retrieve competitors, feature comparison)
    - Skip exhaustive testing of all endpoints
  - [ ] 3.2 Create competitive research controller
    - Actions: POST /competitive-research (trigger), GET /competitive-research/:id (results)
    - Follow pattern from: projects router in api/routers/projects.py
  - [ ] 3.3 Implement agent execution endpoint
    - POST /competitive-research/execute
    - Accept search parameters (industry, geography, keywords)
    - Trigger Competitive Research Agent asynchronously
    - Return execution status, job ID, and tier limits
  - [ ] 3.4 Create competitor retrieval endpoints
    - GET /competitive-research/:id/competitors (list all competitors)
    - GET /competitors/:id (retrieve single competitor with features)
    - Support filtering: by competitor_type, funding range
  - [ ] 3.5 Create feature comparison endpoint
    - GET /competitive-research/:id/feature-matrix (feature comparison matrix)
    - Return structured matrix: rows = competitors, columns = features
  - [ ] 3.6 Create positioning recommendations endpoint
    - GET /competitive-research/:id/positioning (retrieve recommendations)
  - [ ] 3.7 Add authorization and tenant isolation
    - Validate user can only access their org's competitive research
    - Apply RLS policies through org_id context
    - Enforce tier-based search limits
  - [ ] 3.8 Ensure API layer tests pass
    - Run ONLY the 2-8 tests written in 3.1
    - Verify critical CRUD operations work
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 3.1 pass
- Agent execution endpoint triggers research successfully
- Competitor retrieval endpoints return accurate data
- Feature comparison matrix endpoint works
- Positioning recommendations endpoint functional
- Multi-tenant isolation and tier limits enforced

### Frontend Components

#### Task Group 4: Competitive Research UI
**Dependencies:** Task Group 3

- [ ] 4.0 Complete competitive research UI
  - [ ] 4.1 Write 2-8 focused tests for UI components
    - Limit to 2-8 highly focused tests maximum
    - Test critical UI behaviors (competitor cards, feature matrix, search usage indicator)
    - Skip exhaustive testing of all component states
  - [ ] 4.2 Create competitor profile cards component
    - Display competitor name, logo, description, funding, headquarters
    - Show competitor type badge (direct/indirect/substitute)
    - Click to expand full competitor details
  - [ ] 4.3 Create feature comparison matrix component
    - Table view: rows = competitors, columns = features
    - Checkmarks/X marks for feature presence
    - Highlight unique features (only venture has)
    - Highlight gaps (competitors have, venture doesn't)
  - [ ] 4.4 Build positioning recommendations component
    - Display 3-5 positioning strategy cards
    - Show differentiation angle and evidence summary
    - Prioritize by recommendation priority
  - [ ] 4.5 Create search query usage indicator
    - Display queries used vs. tier limit (e.g., "7/10 searches used")
    - Warn when approaching tier limit
    - Upgrade CTA for Starter tier users
  - [ ] 4.6 Build competitive landscape overview
    - Summary: total competitors, competitor type distribution
    - Feature coverage percentage vs. market leaders
    - White space opportunities visualization
  - [ ] 4.7 Apply responsive design
    - Mobile: 320px - 768px (stacked cards)
    - Tablet: 768px - 1024px (grid layout)
    - Desktop: 1024px+ (multi-column layout)
  - [ ] 4.8 Ensure UI component tests pass
    - Run ONLY the 2-8 tests written in 4.1
    - Verify critical component behaviors work
    - Do NOT run the entire test suite at this stage

**Acceptance Criteria:**
- The 2-8 tests written in 4.1 pass
- Competitor profile cards display correctly
- Feature comparison matrix renders accurately
- Positioning recommendations show 3-5 strategies
- Search usage indicator displays tier limits
- Competitive landscape overview functional

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
  - [ ] 5.2 Analyze test coverage gaps for Competitive Research feature only
    - Identify critical user workflows lacking test coverage
    - Focus on end-to-end competitive research workflow (trigger → discover → analyze → view)
    - Prioritize integration tests over unit test gaps
  - [ ] 5.3 Write up to 10 additional strategic tests maximum
    - Add maximum of 10 new tests to fill identified critical gaps
    - Focus on: complete research workflow, tier limit enforcement, feature gap analysis accuracy
    - Do NOT write comprehensive coverage for all scenarios
  - [ ] 5.4 Run feature-specific tests only
    - Run ONLY tests related to Competitive Research feature
    - Expected total: approximately 18-42 tests maximum
    - Do NOT run the entire application test suite
    - Verify critical workflows pass

**Acceptance Criteria:**
- All Competitive Research-specific tests pass (approximately 18-42 tests total)
- Critical competitive research workflow covered end-to-end
- No more than 10 additional tests added when filling gaps
- Testing focused exclusively on Competitive Research feature requirements

## Execution Order

Recommended implementation sequence:
1. Database Layer (Task Group 1) - Competitive research models
2. Agent/AI Layer (Task Group 2) - Competitive research agent with web search
3. API Layer (Task Group 3) - Research execution endpoints
4. Frontend Components (Task Group 4) - Competitive research UI
5. Testing (Task Group 5) - Test review and gap analysis
