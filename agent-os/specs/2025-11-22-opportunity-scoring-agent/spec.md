# Specification: Opportunity Scoring Agent

## Goal

Automate venture opportunity assessment through TAM/SAM/SOM calculation, market growth trajectory analysis, timing evaluation, and composite opportunity score generation to support investment decisions in the Discover phase.

## User Stories

- As a venture creator, I want TAM/SAM/SOM calculated automatically so that I can quantify market opportunity
- As an investor, I want market growth trajectory analysis so that I can assess future potential
- As a strategist, I want an opportunity score so that I can compare venture opportunities objectively

## Specific Requirements

**TAM/SAM/SOM Calculation**
- Calculate Total Addressable Market (TAM) using top-down approach
- Estimate Serviceable Available Market (SAM) with bottom-up validation
- Project Serviceable Obtainable Market (SOM) for 3-year realistic capture
- Validate market sizing with multiple data sources (E2+ evidence tier)

**Market Growth Trajectory Analysis**
- Estimate market CAGR from industry reports and analyst projections
- Identify growth drivers and market expansion factors
- Assess market maturity stage (emerging, growth, mature, declining)
- Project revenue potential over 3-year horizon

**Timing Assessment**
- Evaluate market timing (too early, right time, late)
- Assess technology adoption curves and readiness
- Identify market catalysts and inflection points
- Recommend launch timing based on market dynamics

**Opportunity Score Generation**
- Calculate composite opportunity score (0-100) weighted by size (40%), growth (25%), whitespace (25%), competition penalty (10%)
- Classify opportunity tiers (high >75, medium 50-75, low <50)
- Generate opportunity scorecard with component breakdown
- Compare opportunity score across multiple ventures

**Evidence-Based Scoring**
- Link all market estimates to evidence sources
- Classify evidence confidence using E0-E4 tiers
- Flag assumptions with low evidence confidence (E0/E1)
- Recommend validation activities to upgrade evidence tiers

## Visual Design

No visual assets provided. Design system should follow:
- Opportunity score gauge (0-100) with tier classification
- Market size waterfall chart (TAM → SAM → SOM)
- Growth trajectory line chart over 3 years
- Scorecard breakdown showing size, growth, whitespace, competition scores

## Existing Code to Leverage

**Opportunity Scorer Agent**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Already implemented - validate and extend as needed
- Reuse OpportunityMetrics and OpportunityScoreCard schemas
- Apply existing scoring logic and validation

**Opportunity Metrics Schema**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Use existing OpportunityMetrics model (tam_usd, sam_usd, som_usd, cagr_percent)
- Leverage OpportunityScoreCard.from_metrics() for score calculation
- Extend with additional metrics if needed

**Evidence Integration**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Link market estimates to EvidenceItem records
- Validate evidence tiers for market assumptions
- Track evidence sources for all market claims

## Out of Scope

- Real-time market data feeds (static analysis)
- Proprietary market research databases (public sources only)
- Geographic market breakdown (US focus for MVP)
- Customer segment-specific TAM/SAM/SOM
- Market share forecasting for specific competitors
- Sensitivity analysis on market assumptions
- Monte Carlo simulations for market uncertainty
- Market entry cost modeling (covered in cost modeling feature)
- Regulatory impact on market size
- Technology disruption scenario modeling
