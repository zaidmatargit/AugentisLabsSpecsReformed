# Specification: Market Research Automation

## Goal

Automate comprehensive market research through AI-powered industry analysis, market size validation, and trend identification to support venture definition and positioning in the Define phase.

## User Stories

- As a venture creator, I want automated market research so that I can understand my industry without hiring analysts
- As a product strategist, I want market size validation so that I can confirm TAM/SAM/SOM estimates with third-party data
- As a trend analyst, I want trend identification so that I can position my venture for future market shifts

## Specific Requirements

**Automated Market Research Agent**
- Generate comprehensive market research reports using AI and web search
- Analyze industry structure, key players, and market dynamics
- Extract market insights from industry reports, analyst research, and news
- Target 95%+ agent execution success rate

**Industry Analysis**
- Identify industry trends, drivers, and inhibitors
- Map industry value chain and ecosystem participants
- Analyze regulatory environment and policy impacts
- Assess technology disruption and innovation trends

**Market Size Validation**
- Validate TAM/SAM/SOM estimates from opportunity scoring against third-party data
- Cross-reference multiple sources for market size credibility
- Calculate market size using top-down and bottom-up approaches
- Track market growth projections (CAGR) and trajectory

**Trend Identification**
- Identify emerging trends relevant to venture positioning
- Assess trend maturity (emerging, growing, mainstream, declining)
- Quantify trend impact on target market
- Recommend positioning strategies aligned with trends

**Research Report Generation**
- Generate comprehensive market research report as PDF
- Include executive summary with key findings
- Cite all sources with URLs and publication dates
- Classify evidence tiers for all market claims (E2+ required)

## Visual Design

No visual assets provided. Design system should follow:
- Market research dashboard with key industry metrics
- Trend timeline showing emergence and growth
- Market size waterfall chart (TAM → SAM → SOM)
- Evidence tier badges on all market claims

## Existing Code to Leverage

**Research Scout Agent**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Extend research scout patterns for deeper market analysis
- Reuse web search integration and source extraction
- Apply similar evidence tier classification

**Market Trend Schema**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Reuse MarketTrend model for trend tracking
- Extend with trend maturity and impact metrics
- Link trends to evidence sources

**Project Model Extension**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Create MarketResearch entity linked to Project
- Store research findings and source citations
- Apply multi-tenant RLS for research data isolation

## Out of Scope

- Primary market research (surveys, interviews) - secondary research only
- Proprietary research database access (public sources only)
- Real-time market monitoring and alerts (one-time research)
- Competitor intelligence beyond public information
- Customer segmentation research (covered in persona generation)
- Pricing research and willingness-to-pay analysis
- Brand perception and sentiment analysis
- Market entry strategy beyond sizing and trends
- International market research (US focus for MVP)
- Longitudinal market tracking over time
