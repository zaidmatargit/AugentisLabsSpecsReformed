# MarketOpportunityArtifact

Captures TAM/SAM/SOM and timing/trend analysis. Produced by the Opportunity Scorer agent and mapped to VRC indicators 7–10 (Market Intelligence).[attached_file:1]

## Pydantic model

from typing import List, Optional, Literal
from datetime import datetime
from pydantic import BaseModel

class MarketSizeEstimate(BaseModel):
label: Literal["TAM", "SAM", "SOM"]
value: float # numeric value (e.g. 1_500_000_000)
currency: Optional[str] = None # e.g. "USD"
units: str # e.g. "annual_revenue", "number_of_companies"
method: str # e.g. "top_down", "bottom_up"
key_assumptions: List[str] # list of assumption strings
evidence_tier: EvidenceTier
notes: Optional[str] = None

class TrendInsight(BaseModel):
category: str # "technology", "regulatory", "economic", "behavioral"
description: str
direction: Optional[str] = None # e.g. "supportive", "neutral", "negative"
evidence_tier: EvidenceTier
sources: Optional[List[str]] = None

class IndicatorEvidence(BaseModel):
indicator_id: str
tier: EvidenceTier
score_0_to_100: int
notes: Optional[str] = None
next_steps: Optional[str] = None

class MarketOpportunityArtifact(BaseModel):
artifact_id: str
version: str = "0.1"
created_at: datetime
updated_at: datetime

problem_artifact_id: str
competitive_artifact_id: str

tam: MarketSizeEstimate
sam: Optional[MarketSizeEstimate] = None
som: Optional[MarketSizeEstimate] = None

trends: List[TrendInsight]

# VRC linkage for indicators 7–10

market_timing_indicator: IndicatorEvidence # I7
regulatory_indicator: IndicatorEvidence # I8
technology_trends_indicator: IndicatorEvidence # I9
economic_factors_indicator: IndicatorEvidence # I10

scenario_notes: Optional[str] = None # brief description of conservative/base/optimistic cases
additional_notes: Optional[str] = None

## Field descriptions

### MarketOpportunityArtifact

| Field                       | Type                | Description                                                                              |
| --------------------------- | ------------------- | ---------------------------------------------------------------------------------------- |
| artifact_id                 | string              | Unique ID for this market opportunity artifact.                                          |
| version                     | string              | Version label for progressive updates.                                                   |
| created_at                  | datetime            | Creation timestamp.                                                                      |
| updated_at                  | datetime            | Last update timestamp.                                                                   |
| problem_artifact_id         | string              | ID of the related ProblemStatementArtifact.                                              |
| competitive_artifact_id     | string              | ID of the related CompetitiveLandscapeArtifact.                                          |
| tam                         | MarketSizeEstimate  | Total Addressable Market estimate and its assumptions.                                   |
| sam                         | MarketSizeEstimate? | Serviceable Available Market estimate.                                                   |
| som                         | MarketSizeEstimate? | Serviceable Obtainable Market estimate.                                                  |
| trends                      | list[TrendInsight]  | Key market trends (tech, regulatory, economic, behavioral) relevant to this opportunity. |
| market_timing_indicator     | IndicatorEvidence   | VRC indicator 7 (market timing) evidence snapshot.[attached_file:1]                      |
| regulatory_indicator        | IndicatorEvidence   | VRC indicator 8 (regulatory environment) evidence snapshot.                              |
| technology_trends_indicator | IndicatorEvidence   | VRC indicator 9 (technology trends alignment) evidence snapshot.                         |
| economic_factors_indicator  | IndicatorEvidence   | VRC indicator 10 (economic factors) evidence snapshot.                                   |
| scenario_notes              | string?             | Narrative of conservative/base/optimistic market scenarios.                              |
| additional_notes            | string?             | Extra context or caveats.                                                                |

### MarketSizeEstimate

| Field           | Type              | Description                                                        |
| --------------- | ----------------- | ------------------------------------------------------------------ |
| label           | "TAM"/"SAM"/"SOM" | Which market layer this estimate refers to.                        |
| value           | float             | Numeric estimate (e.g. revenue or number of accounts).             |
| currency        | string?           | Currency if value is monetary (e.g. “USD”).                        |
| units           | string            | Unit of measurement, e.g. “annual_revenue”, “number_of_companies”. |
| method          | string            | Approach, e.g. “top_down”, “bottom_up”.                            |
| key_assumptions | list[string]      | Explicit assumptions underlying the estimate.                      |
| evidence_tier   | EvidenceTier      | Strength of evidence backing this estimate.                        |
| notes           | string?           | Additional commentary or caveats.                                  |

### TrendInsight

| Field         | Type          | Description                                                             |
| ------------- | ------------- | ----------------------------------------------------------------------- |
| category      | string        | Type of trend: “technology”, “regulatory”, “economic”, or “behavioral”. |
| description   | string        | Description of the trend and its relevance.                             |
| direction     | string?       | Qualitative direction: “supportive”, “neutral”, or “negative”.          |
| evidence_tier | EvidenceTier  | How strong the supporting evidence is for this trend.                   |
| sources       | list[string]? | Data sources used to identify/validate the trend.                       |
