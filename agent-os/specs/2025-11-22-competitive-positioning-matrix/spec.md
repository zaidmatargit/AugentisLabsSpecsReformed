# Specification: Competitive Positioning Matrix

## Goal

Generate feature comparison matrices and competitive positioning maps from research data, enabling clear differentiation analysis and competitive advantage identification for venture positioning in the Define phase.

## User Stories

- As a product strategist, I want a feature comparison matrix so that I can see how our offering compares to competitors at a glance
- As a venture creator, I want a positioning map so that I can visualize market gaps and differentiation opportunities
- As a sales team member, I want competitive advantage documentation so that I can articulate our unique value in customer conversations

## Specific Requirements

**Feature Comparison Matrix**
- Generate side-by-side feature comparison table for top 5-10 competitors
- Extract features from competitive research agent outputs
- Categorize features by product area (authentication, core features, integrations, etc.)
- Use yes/partial/no indicators with supporting evidence links

**Positioning Map Generation**
- Create 2D positioning maps on key differentiation axes (price vs features, simplicity vs power, etc.)
- Plot competitors on positioning map based on research data
- Identify white space opportunities for differentiation
- Generate multiple positioning map variants exploring different axes

**Differentiation Analysis**
- Identify unique features not offered by competitors
- Quantify feature gaps where competitors are stronger
- Recommend differentiation strategies based on market gaps
- Prioritize differentiators by customer importance (from persona research)

**Competitive Advantage Identification**
- Extract sustainable competitive advantages from feature analysis
- Classify advantages by type (technology moat, network effects, brand, etc.)
- Validate advantages against evidence tier requirements (E2+ evidence)
- Document competitive risks and mitigation strategies

**Matrix Export and Visualization**
- Generate comparison matrix as markdown table and HTML
- Export positioning maps as SVG/PNG visualizations
- Create executive summary of competitive landscape
- Enable filtering and customization of comparison dimensions

## Visual Design

No visual assets provided. Design system should follow:
- Comparison matrix table with color-coded cells (green=yes, yellow=partial, red=no)
- 2D scatter plot for positioning maps with competitor logos/names
- Feature gap highlight cards showing areas for improvement
- Competitive advantage summary cards with evidence badges

## Existing Code to Leverage

**Competitive Research Integration**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Reuse CompanyProfile and FeatureSupportEntry models
- Extract feature matrix data from ResearchOutputs
- Link to evidence from competitive research agent

**LangGraph Agent Pattern**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Create Positioning Matrix Agent for analysis synthesis
- Use structured output for matrix and map generation
- Implement state management for iterative refinement

**Project Model Extension**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Create CompetitiveMatrix model associated with Project
- Store matrix data and positioning coordinates
- Apply multi-tenant RLS for competitive data isolation

## Out of Scope

- Real-time competitive intelligence monitoring (static analysis only)
- Integration with competitive intelligence platforms (Crayon, Klue)
- Automated competitor feature tracking over time
- SWOT analysis generation (separate strategic analysis feature)
- Pricing comparison and pricing strategy recommendations
- Market share analysis and growth trajectory comparison
- Competitive response playbooks and battle cards
- Win/loss analysis integration with CRM data
- Competitive positioning testing with target customers
- Multi-dimensional positioning beyond 2D maps
