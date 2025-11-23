# Specification: Competitive Research Agent

## Goal

Automate competitive landscape mapping through AI-powered web search integration, feature gap analysis, and positioning recommendations to identify market opportunities and competitive threats in the Discover phase.

## User Stories

- As a venture creator, I want automated competitive research so that I can understand the competitive landscape without manual analysis
- As a product manager, I want feature gap analysis so that I can identify unmet needs and differentiation opportunities
- As a strategist, I want positioning recommendations so that I can articulate our unique value proposition relative to competitors

## Specific Requirements

**Competitive Landscape Mapping**
- Identify top 10-15 competitors through AI-powered web search
- Extract competitor profiles (name, website, funding, headquarters, description)
- Categorize competitors by type (direct, indirect, substitute solutions)
- Map competitor features using structured data extraction

**Feature Gap Analysis**
- Compare venture's proposed features against competitor offerings
- Identify feature gaps where competitors are ahead
- Highlight unique features not offered by competitors
- Quantify feature coverage percentage vs. market leaders

**Positioning Recommendations**
- Generate 3-5 positioning strategy recommendations based on competitive analysis
- Identify white space opportunities in the market
- Suggest differentiation angles (technology, pricing, target segment, etc.)
- Validate positioning against evidence from market research

**Web Search Integration**
- Integrate with search APIs for automated competitor discovery
- Implement tiered search limits (10/50/unlimited queries by tier: Starter/Professional/Enterprise)
- Cache search results to minimize API costs
- Extract structured data from competitor websites and public sources

**AI Agent Orchestration**
- Implement as LangGraph agent with state management
- Use OpenAI/Anthropic LLMs for analysis synthesis
- Target 95%+ agent execution success rate
- Maintain <5K tokens per competitive analysis

## Visual Design

No visual assets provided. Design system should follow:
- Competitor profile cards with logos, descriptions, and feature lists
- Feature comparison matrix table
- Positioning strategy recommendation cards
- Search query usage indicator showing tier limits

## Existing Code to Leverage

**LangGraph Agent Pattern**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Reuse StateGraph orchestration pattern
- Apply similar structured output validation
- Implement comparable error handling

**Research Scout Integration**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Extend CompanyProfile, FeatureSupportEntry models
- Reuse ResearchOutputs structure for competitive data
- Apply same evidence tier classification

**Project Model Association**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Link competitive research to Project entity
- Store competitor data with version tracking
- Apply multi-tenant RLS for competitive intelligence isolation

## Out of Scope

- Real-time competitor monitoring and alerts (static analysis only)
- Social media sentiment analysis of competitors
- Competitor pricing intelligence beyond publicly available data
- Competitor customer reviews aggregation and analysis
- Integration with third-party competitive intelligence platforms
- Patent and IP landscape analysis
- Competitor financial analysis beyond public funding data
- Automated competitor website scraping (ethical web search only)
- Competitor marketing strategy analysis
- Market share estimation and growth projections
