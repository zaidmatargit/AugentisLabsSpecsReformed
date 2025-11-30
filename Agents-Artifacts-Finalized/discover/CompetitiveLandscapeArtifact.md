# CompetitiveLandscapeArtifact

Describes who else is solving the problem, how, and where the white space is. Produced by the Competitive Analyzer agent and mapped mainly to VRC indicator 6 (Competitive Landscape Understanding).[attached_file:1]

## Pydantic model

from enum import Enum
from typing import List, Optional
from datetime import datetime
from pydantic import BaseModel

class CompetitorType(str, Enum):
DIRECT = "direct"
INDIRECT = "indirect"
SUBSTITUTE = "substitute"

class PricingModel(str, Enum):
UNKNOWN = "unknown"
SAAS_SUBSCRIPTION = "saas_subscription"
ONE_TIME = "one_time"
FREEMIUM = "freemium"
USAGE_BASED = "usage_based"
OTHER = "other"

class Competitor(BaseModel):
name: str
url: Optional[str] = None
competitor_type: CompetitorType
brief_description: Optional[str] = None
strengths: Optional[List[str]] = None
weaknesses: Optional[List[str]] = None
pricing_model: Optional[PricingModel] = PricingModel.UNKNOWN
price_range: Optional[str] = None # e.g. "$50–$500/month"

class FeatureComparisonRow(BaseModel):
feature_name: str
description: Optional[str] = None
competitors_supporting: List[str] # competitor names
notes: Optional[str] = None

class PositioningGap(BaseModel):
gap_description: str # e.g. "No tool focused on churn root-cause for DTC subs"
supporting_evidence: Optional[str] = None
potential_angle: Optional[str] = None # e.g. "Specialized churn analytics for subscription brands"

class IndicatorEvidence(BaseModel):
indicator_id: str
tier: EvidenceTier
score_0_to_100: int
notes: Optional[str] = None
next_steps: Optional[str] = None

class CompetitiveLandscapeArtifact(BaseModel):
artifact_id: str
version: str = "0.1"
created_at: datetime
updated_at: datetime

problem_artifact_id: str # Link back to ProblemStatementArtifact

competitors: List[Competitor]
feature_matrix: List[FeatureComparisonRow]
positioning_gaps: List[PositioningGap]

competitive_understanding_evidence: IndicatorEvidence # VRC I6
additional_notes: Optional[str] = None

## Field descriptions

### CompetitiveLandscapeArtifact

| Field                              | Type                       | Description                                                                  |
| ---------------------------------- | -------------------------- | ---------------------------------------------------------------------------- |
| artifact_id                        | string                     | Unique ID for this competitive landscape artifact.                           |
| version                            | string                     | Version label for progressive updates.                                       |
| created_at                         | datetime                   | Creation timestamp.                                                          |
| updated_at                         | datetime                   | Last update timestamp.                                                       |
| problem_artifact_id                | string                     | ID of the related ProblemStatementArtifact.                                  |
| competitors                        | list[Competitor]           | List of direct, indirect, and substitute competitors.                        |
| feature_matrix                     | list[FeatureComparisonRow] | Tabular feature comparison across competitors.                               |
| positioning_gaps                   | list[PositioningGap]       | Identified white-space / differentiation opportunities.[attached_file:1]     |
| competitive_understanding_evidence | IndicatorEvidence          | Evidence snapshot for VRC indicator 6 (competitive landscape understanding). |
| additional_notes                   | string?                    | Free-form commentary or caveats.                                             |

### Competitor

| Field             | Type           | Description                              |
| ----------------- | -------------- | ---------------------------------------- |
| name              | string         | Competitor name.                         |
| url               | string?        | Main website or product URL.             |
| competitor_type   | CompetitorType | `direct`, `indirect`, or `substitute`.   |
| brief_description | string?        | Short description of what they do.       |
| strengths         | list[string]?  | High-level strengths vs your concept.    |
| weaknesses        | list[string]?  | High-level weaknesses or gaps.           |
| pricing_model     | PricingModel?  | Pricing model classification.            |
| price_range       | string?        | Rough price band, e.g. “$50–$500/month”. |

### FeatureComparisonRow

| Field                  | Type         | Description                                       |
| ---------------------- | ------------ | ------------------------------------------------- |
| feature_name           | string       | Name of the feature/capability being compared.    |
| description            | string?      | Short explanation of the feature.                 |
| competitors_supporting | list[string] | Names of competitors that support this feature.   |
| notes                  | string?      | Any relevant notes about differences/limitations. |

### PositioningGap

| Field               | Type    | Description                                                         |
| ------------------- | ------- | ------------------------------------------------------------------- |
| gap_description     | string  | Description of the gap or unmet need observed in the market.        |
| supporting_evidence | string? | Evidence supporting this gap (e.g. user complaints, reviews).       |
| potential_angle     | string? | Suggested differentiation or positioning angle to exploit this gap. |
