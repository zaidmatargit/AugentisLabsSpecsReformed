# Specification: Research Scout Agent

## Goal

Automate market research, trend analysis, industry report aggregation, and source credibility scoring to provide comprehensive market intelligence in the Discover phase.

## User Stories

- As a venture creator, I want automated market research so that I can understand my market without manual searching
- As an analyst, I want trend analysis so that I can position my venture for future market shifts
- As a researcher, I want source credibility scoring so that I can trust the data I'm using

## Specific Requirements

**Automated Market Research**
- Search public web sources for market data, industry reports, and competitive intelligence
- Extract structured data from research sources (market size, growth rates, key players)
- Aggregate insights from multiple sources for comprehensive coverage
- Target 95%+ agent execution success rate with <5K tokens per research run

**Trend Analysis**
- Identify emerging trends relevant to target market
- Assess trend strength and trajectory (emerging, growing, peaking, declining)
- Extract trend indicators (adoption rates, investment activity, media mentions)
- Link trends to venture positioning opportunities

**Industry Report Aggregation**
- Discover industry reports from research firms (Gartner, Forrester, IDC, etc.)
- Extract key findings and statistics from reports
- Summarize report conclusions and recommendations
- Track report publication dates and recency

**Source Credibility Scoring**
- Score source credibility based on authority, recency, and corroboration
- Prioritize primary sources over secondary sources
- Flag outdated sources (>2 years old) for validation
- Cross-reference data across multiple sources for validation

**Research Output Structuring**
- Generate structured research outputs (competitor profiles, feature matrix, trends, regulations)
- Link all research findings to evidence tier classification
- Provide source URLs and citations for all claims
- Export research as markdown report

## Visual Design

No visual assets provided. Design system should follow:
- Research dashboard with key findings cards
- Trend timeline showing emergence and growth
- Source credibility badges (high, medium, low confidence)
- Research report viewer with citations

## Existing Code to Leverage

**Research Scout Agent**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Already implemented - validate and extend as needed
- Reuse _research_scout method and ResearchOutputs schema
- Apply existing web search integration patterns

**Research Outputs Schema**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Use existing CompanyProfile, MarketTrend, RegulationItem models
- Leverage FeatureSupportEntry for feature comparison
- Extend with credibility scoring metadata

**Evidence Integration**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Generate EvidenceItem records from research findings
- Classify evidence tiers based on source credibility
- Track sources for all research claims

## Out of Scope

- Proprietary research database access (public web only)
- Real-time market monitoring and alerts (one-time research)
- Primary research execution (surveys, interviews) - secondary research only
- Academic paper search and analysis (focus on industry reports)
- Patent and IP research
- Financial data and stock market analysis
- Social media sentiment analysis (basic trend analysis only)
- Expert network integration for validation
- Custom research requests beyond automated discovery
- Multi-language research (English only for MVP)
