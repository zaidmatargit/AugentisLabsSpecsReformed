# Discover Phase – Agent Specification Document

## Overview
The Discover phase consists of four specialized agents working in sequence to validate a user's idea/problem, analyze market conditions, and assess venture readiness. Each agent produces progressive artifacts (v0.1, v0.2, etc.) as new evidence is added.

---

## Agent 1: Problem Validator

### Purpose
Normalize, clarify, and validate the user's problem statement and customer evidence against VRC indicators 1–5 (Problem Understanding pillar).

### Skills & Capabilities
- Problem statement normalization (converts messy user input into structured, one-sentence problem)
- Customer interview parsing and synthesis
- Pain point quantification (time, cost, frequency, emotional impact)
- Evidence tier classification (E0–E4)
- Gap analysis (identifies which VRC indicators 1–5 are weak and what evidence is needed)

### Inputs
- Raw problem description (unstructured text or narrative)
- Target customer description (role, company type/size, geography)
- Existing evidence:
  - Interview notes/transcripts (count and summaries)
  - Survey data (response counts, key findings)
  - Observational data (product metrics, churn rates, etc.)
  - Social/forum snippets (if user has collected)
- Constraints or hypotheses (optional)

### Tools & Integrations
- Text normalization and NLP (for problem clarity scoring)
- Interview note parser (extracts pain signals, frequency, severity)
- Evidence tier classifier (maps qualitative/quantitative data to E0–E4 scale)
- VRC indicator mapper (links evidence to indicators 1–5)

### Outputs (Progressive Artifacts)

**Primary Artifact: Problem Statement Document v0.x**
- One-sentence problem statement
- Customer segment description (who is affected, where)
- Context and triggers (when/where problem occurs)
- Qualitative pain summary (from interviews/observations)
- Quantified impact (time/cost/frequency if available)
- Evidence tier mapping for VRC indicators 1–5
- Gaps list (what evidence is missing to improve indicators)

**Secondary Output: Evidence Checklist**
- Problem clarity: current tier + next steps
- Severity & urgency: current tier + next steps
- Market size validation: current tier + next steps
- Target customer ID: current tier + next steps
- Pain quantification: current tier + next steps

### Behavior & Update Pattern
- **v0.1 (initial)**: Creates a lightweight Problem Statement from raw input, marks evidence tiers (likely E0–E2), and flags missing data.
- **v0.2+ (updates)**: When new interview/survey data arrives, re-parses and re-quantifies only the affected sections. Preserves structure and versioning so changes are auditable.
- **Follow-up questions**: Asks specific, minimal questions to close evidence gaps (e.g., "Of the 10 interviews, how many mentioned churn as a top-3 concern?" or "What is the estimated revenue impact per customer lost?").

---

## Agent 2: Competitive Analyzer

### Purpose
Map the competitive landscape, identify existing solutions, feature patterns, and positioning opportunities. Validates market understanding against VRC indicator 6 (Competitive Landscape Understanding).

### Skills & Capabilities
- Competitor identification and research
- Feature/capability analysis and comparison
- Pricing and business model extraction
- Positioning gap identification
- Substitutes and indirect competition analysis
- Evidence tier classification for competitive intelligence

### Inputs
- Problem Statement Document (from Problem Validator)
- Target customer description
- Optional: any competitor names or URLs user has researched

### Tools & Integrations
- Web research and scraping (competitor websites, reviews, pricing pages)
- Feature matrix generator (compares capabilities across competitors)
- Pricing intelligence (extracts business models, pricing tiers)
- Social listening (optional: finds unmet need signals in community chatter)
- Evidence tier classifier (E0–E5 for competitive understanding)

### Outputs (Progressive Artifacts)

**Primary Artifact: Competitive Landscape Report v0.x**
- Top 3–5 direct competitors (with descriptions)
- Substitute/indirect competitors (platforms people use instead)
- Feature comparison matrix (key capabilities each competitor has/lacks)
- Pricing and business model summary (SaaS, one-time, freemium, etc.)
- Positioning gaps (white space opportunities)
- Evidence tier for competitive understanding
- Confidence level and data sources used

**Secondary Output: Positioning Opportunity Map**
- Identified gaps (what competitors don't do well)
- Potential angles for differentiation
- Market signals (unmet needs from user feedback/reviews)

### Behavior & Update Pattern
- **v0.1 (initial)**: Creates a light competitive snapshot (top 3–5 competitors, basic feature grid, rough positioning notes). Likely E2 tier.
- **v0.2+ (updates)**: Enriches with additional competitors, pricing data, or deeper feature analysis as user provides links or feedback. Preserves original competitors so v1 vs v2 remains comparable.
- **Note**: Does not prescribe what the user should do; only surfaces what exists and what gaps appear.

---

## Agent 3: Opportunity Scorer

### Purpose
Assess market opportunity size and timing, including TAM/SAM/SOM estimation, trend analysis, and go-to-market readiness. Validates market intelligence against VRC indicators 7–10 (Market Intelligence pillar) and parts of 11–15 (Solution Validation).

### Skills & Capabilities
- TAM/SAM/SOM calculation (top-down and bottom-up methods)
- Market sizing assumptions (explicit, traceable)
- Trend analysis (technology, regulatory, economic, behavioral)
- Timing and go-to-market assessment
- Evidence tier classification (E0–E5)
- Sensitivity analysis (what if TAM assumptions change?)

### Inputs
- Problem Statement Document (from Problem Validator)
- Competitive Landscape Report (from Competitive Analyzer)
- Target customer segment and size (estimated or researched)
- Optional: solution concept or any go-to-market constraints

### Tools & Integrations
- Market data sources (industry reports, TAM databases, census/SIC data)
- TAM/SAM/SOM calculator (formula-based with assumption tracking)
- Trend analyzer (monitors tech/regulatory/economic signals)
- Evidence tier classifier (E0–E5 for market validation)
- Sensitivity/scenario modeling

### Outputs (Progressive Artifacts)

**Primary Artifact: Market Opportunity Assessment v0.x**
- TAM (Total Addressable Market) with calculation method and key assumptions
- SAM (Serviceable Available Market) with geographic/segment scope
- SOM (Serviceable Obtainable Market) with conservative growth projections
- Assumptions list (clearly labeled, with confidence levels)
- Evidence tiers per assumption (which TAM numbers are E1 vs E3 vs E4?)
- Trend analysis (tech, regulatory, economic, competitive dynamics)
- Go-to-market timing assessment (why now? what enablers?)
- Data sources and citations

**Secondary Output: Scenario Analysis**
- Conservative case (lower TAM, slower adoption)
- Base case (mid-range assumptions)
- Optimistic case (higher TAM, faster adoption)

### Behavior & Update Pattern
- **v0.1 (initial)**: Produces a coarse TAM/SAM/SOM with heavily labeled assumptions and low evidence tiers (E1–E2). Likely includes public benchmark data and simple extrapolation.
- **v0.2+ (updates)**: Refines numbers and assumptions as user provides industry reports, market data, or customer feedback. Tracks which assumptions moved from E1→E3 vs stayed unchanged. Never hides earlier assumptions—shows the evolution.
- **Dependency**: Waits for Problem Statement v0.1 and Competitive Landscape v0.1 before running first time, but can update independently if new market data arrives.

---

## Agent 4: VRC Assessor

### Purpose
Conduct a comprehensive venture readiness check across all 20 indicators (Problem Understanding, Market Intelligence, Solution Validation, Team Resources). Score readiness, recommend phase gate decision, and highlight improvement priorities.

### Skills & Capabilities
- 20-indicator VRC questionnaire evaluation
- Evidence tier assessment (E0–E4 rubric per indicator)
- Composite scoring (weighted average across all 20 indicators)
- Phase gate logic (Pass ≥70, Conditional 60–69, Block <60)
- Improvement roadmap generation (prioritized next steps per indicator)
- Report generation (executive summary, detailed analysis, action plans)

### Inputs
- Problem Statement Document v0.x (from Problem Validator)
- Competitive Landscape Report v0.x (from Competitive Analyzer)
- Market Opportunity Assessment v0.x (from Opportunity Scorer)
- Team/Resource info (optional; user can provide or skip for lighter assessment)
- Solution concept (optional; if provided, helps assess indicators 11–15)

### Tools & Integrations
- VRC questionnaire engine (20 indicators with E0–E5 scoring rubrics)
- Evidence evaluator (determines evidence tier per indicator)
- Composite scorer (calculates overall VRC score and gate decision)
- Report generator (produces executive summary, detailed analysis, action plans, PDF export)
- Feedback tracker (notes which indicators improved v0.1→v0.2)

### Outputs (Progressive Artifacts)

**Primary Artifact: VRC Assessment Report v0.x**
- Executive Summary
  - Overall VRC score (0–100)
  - Phase gate decision (Pass / Conditional Pass / Block)
  - Key strengths (which indicator clusters are strong?)
  - Key risks (which are weakest?)
  
- Detailed Assessment (per 20-indicator pillar)
  - Problem Understanding (5 indicators)
  - Market Intelligence (5 indicators)
  - Solution Validation (5 indicators)
  - Team Resources (5 indicators)
  - For each: evidence tier, scoring rubric match, confidence level, improvement gap
  
- Strategic Action Plan
  - Prioritized improvement roadmap (what to address first to move gate from Block→Conditional or Conditional→Pass)
  - Evidence collection tasks (e.g., "Conduct 10–15 structured interviews to move Problem Severity from E1→E3")
  - Timeline estimate
  - Resource requirements
  
- Supporting Documentation
  - Evidence inventory (what we have, what we don't)
  - Benchmark comparison (how this venture compares to past assessments)
  - Historical tracking (v0.1 vs v0.2 delta)

**Secondary Output: Improvement Tracker**
- Indicators that moved (with tiers and delta)
- Indicators that stayed blocked (with next steps)
- Confidence trend (improving, stable, or declining?)

### Behavior & Update Pattern
- **v0.1 (initial)**: Produces a full 20-indicator assessment using whatever evidence exists, even if sparse. Likely yields a lower overall score (40–60) and clear recommendations on which indicators to strengthen.
- **v0.2+ (updates)**: Re-scores only the indicators whose upstream evidence changed (triggered by new Problem Statement, Competitive Landscape, or Market Assessment versions). Shows a delta view (what improved, what stayed weak). Overall score adjusts accordingly.
- **Dependency**: Requires at least Problem Statement v0.1 and Competitive Landscape v0.1 to run. Can run without Market Opportunity Assessment, but will flag market-related indicators as weaker.
- **Gate Logic**: Does not auto-advance to next phase; recommends gate decision and waits for explicit user/team decision.

---

## Cross-Agent Orchestration

### Recommended Call Order
1. **Problem Validator** (v0.1) – must run first
2. **Competitive Analyzer** (v0.1) – can run after Problem Validator v0.1
3. **Opportunity Scorer** (v0.1) – can run after Competitive Analyzer v0.1
4. **VRC Assessor** (v0.1) – runs after the above three have produced v0.1

### Update Triggers
- User submits new evidence → Problem Validator updates to v0.2
- Problem Validator reaches v0.2 → Competitive Analyzer can optionally re-run for v0.2 (if competitive analysis was shallow)
- Competitive Analyzer reaches v0.2 → Opportunity Scorer can re-run for v0.2 (if market data improved)
- Any agent reaches v0.2+ → VRC Assessor automatically re-runs for v0.2

### Artifact Handoff Pattern
- Problem Statement v0.x → input to Competitive Analyzer and Opportunity Scorer
- Competitive Landscape v0.x → input to Opportunity Scorer and VRC Assessor
- Market Opportunity Assessment v0.x → input to VRC Assessor
- All three → input to VRC Assessor for final scoring

---

## Key Principles

1. **Progressive, not iterative**: Artifacts are versioned (v0.1, v0.2, …) and updated, not deleted and recreated.
2. **Evidence-driven**: Every agent maps output to VRC indicators and evidence tiers (E0–E4) so scoring is transparent.
3. **Structured, not prescriptive**: Agents expect clean input but can work with messy data. They identify gaps and ask follow-up questions instead of dictating how to find answers.
4. **Minimal follow-ups**: Agents only ask for what is needed to improve specific VRC indicators, not for "perfect" inputs.
5. **Transparent assumptions**: Market Opportunity Assessment and VRC Report always show assumptions, sources, and confidence levels so users can audit and improve them.
