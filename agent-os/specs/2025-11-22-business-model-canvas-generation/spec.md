# Specification: Business Model Canvas Generation

## Goal

Automatically generate Business Model Canvas documents from venture research data, providing structured business model visualization with value propositions, revenue models, and cost structures to support strategic planning in the Define phase.

## User Stories

- As a venture creator, I want an automated Business Model Canvas so that I can visualize my business model without manual synthesis
- As an investor, I want to see value proposition and revenue model clearly so that I can evaluate business viability quickly
- As a strategy consultant, I want cost structure analysis so that I can identify unit economics and profitability drivers

## Specific Requirements

**Business Model Canvas Structure**
- Generate all 9 BMC building blocks: Customer Segments, Value Propositions, Channels, Customer Relationships, Revenue Streams, Key Resources, Key Activities, Key Partnerships, Cost Structure
- Extract data from VRC assessment, market research, and persona evidence
- Ensure each block has 3-5 concrete elements backed by E2+ evidence
- Link BMC elements to source evidence for traceability

**Value Proposition Refinement**
- Synthesize value propositions from problem validation and persona jobs-to-be-done
- Map value propositions to specific customer segments
- Quantify value using outcome metrics where possible
- Differentiate from competitor value propositions

**Revenue Model Suggestions**
- Recommend revenue model types (subscription, transaction, freemium, etc.) based on market research
- Estimate pricing ranges based on competitive analysis
- Project revenue potential using TAM/SAM/SOM from opportunity scoring
- Include monetization timeline and scaling assumptions

**Cost Structure Analysis**
- Estimate development costs from technical feasibility assessment
- Identify fixed vs variable costs
- Calculate unit economics (CAC, LTV) based on market assumptions
- Highlight cost optimization opportunities

**Canvas Export and Visualization**
- Generate Business Model Canvas as visual diagram (SVG/PNG)
- Export as editable format (markdown, PDF)
- Create interactive web-based canvas editor
- Enable stakeholder collaboration and annotation

## Visual Design

No visual assets provided. Design system should follow:
- Standard Business Model Canvas 9-block layout
- Color-coded blocks for different canvas sections
- Evidence tier badges showing data confidence per block
- Editable fields with inline editing capabilities

## Existing Code to Leverage

**LangGraph Agent Pattern**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Create BMC Generation Agent following StateGraph pattern
- Orchestrate evidence extraction from multiple sources
- Use structured output for BMC block generation

**Evidence Integration**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Link BMC elements to EvidenceItem records
- Validate evidence tiers for business assumptions
- Track evidence sources for each BMC block

**Project Model Extension**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Create BusinessModelCanvas model associated with Project
- Store canvas data as structured JSON with version history
- Apply multi-tenant RLS for canvas data isolation

## Out of Scope

- Lean Canvas or other canvas variations (BMC only)
- Financial modeling beyond high-level revenue/cost estimates
- Live BMC collaboration with real-time multi-user editing
- BMC templates by industry or venture type
- Integration with business planning software (LivePlan, Strategyzer)
- Competitor BMC comparison visualization
- BMC evolution tracking over time with diff visualization
- Export to presentation formats (PowerPoint, Keynote)
- BMC validation workshops or facilitation guides
- Automated BMC updates based on new evidence
