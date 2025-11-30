# ProblemStatementArtifact

Captures a clear, structured view of the problem, who it affects, and how severe it is. This artifact is produced and updated by the Problem Validator agent and maps directly to VRC indicators 1–5 (Problem Understanding pillar).[attached_file:1]

## Purpose

- Normalize a messy idea/problem into a concise, one-sentence problem statement.
- Describe the target customer segment and context where the problem occurs.
- Summarize qualitative and quantitative pain.
- Attach evidence tiers and “next steps” for indicators I1–I5.[attached_file:1]

## Pydantic model

from enum import Enum
from typing import Optional, Dict, List
from datetime import datetime
from pydantic import BaseModel, Field

class EvidenceTier(str, Enum):
E0 = "E0" # No evidence
E1 = "E1" # Anecdotal
E2 = "E2" # Limited research
E3 = "E3" # Validated research
E4 = "E4" # Strong / proven

class VRCIndicatorId(str, Enum):
PROBLEM_CLARITY = "I1_problem_clarity"
PROBLEM_SEVERITY = "I2_problem_severity"
MARKET_SIZE_VALIDATION = "I3_market_size_validation"
TARGET_CUSTOMER_IDENTIFICATION = "I4_target_customer_identification"
PAIN_POINT_QUANTIFICATION = "I5_pain_point_quantification"

class IndicatorEvidence(BaseModel):
indicator_id: VRCIndicatorId
tier: EvidenceTier # Current evidence tier for this indicator
score_0_to_100: int = Field(ge=0, le=100)
notes: Optional[str] = None # Why this tier/score was assigned
next_steps: Optional[str] = None # What to do to improve this indicator

class QuantifiedImpact(BaseModel):
time_lost_per_user_per_period_hours: Optional[float] = None
cost_impact_per_user_per_period: Optional[float] = None
currency: Optional[str] = None # e.g. "USD"
frequency: Optional[str] = None # e.g. "daily", "weekly"
notes: Optional[str] = None # Any assumptions/context

class CustomerSegment(BaseModel):
label: str # e.g. "DTC subscription brands"
role: Optional[str] = None # e.g. "Founder"
company_size_range: Optional[str] = None
revenue_range: Optional[str] = None
geography: Optional[str] = None

class ProblemStatementArtifact(BaseModel):
artifact_id: str
version: str = "0.1"
created_at: datetime
updated_at: datetime

# Core content

problem_sentence: str # One-sentence, normalized problem
long_description: Optional[str] = None
target_customer: CustomerSegment
context_and_triggers: Optional[str] = None # Where/when the problem occurs
current_workarounds: Optional[str] = None # How people cope today

qualitative_pain_summary: Optional[str] = None
quantified_impact: Optional[QuantifiedImpact] = None

# Evidence and VRC linkage

evidence_sources: Optional[List[str]] = None # e.g. "3 interviews", "internal report"
indicators: Dict[VRCIndicatorId, IndicatorEvidence]

# Meta

author: Optional[str] = None
notes: Optional[str] = None

## Field descriptions

### ProblemStatementArtifact

| Field                    | Type                                    | Description                                                                                                                                             |
| ------------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| artifact_id              | string                                  | Unique ID for this Problem Statement artifact.                                                                                                          |
| version                  | string                                  | Version label, e.g. `\"0.1\"`, allowing progressive updates.                                                                                            |
| created_at               | datetime                                | When this artifact was first created.                                                                                                                   |
| updated_at               | datetime                                | When this artifact was last updated.                                                                                                                    |
| problem_sentence         | string                                  | One-sentence, normalized description of the problem.                                                                                                    |
| long_description         | string?                                 | Optional longer narrative or context for the problem.                                                                                                   |
| target_customer          | CustomerSegment                         | Structured description of the main customer segment affected by this problem.[attached_file:1]                                                          |
| context_and_triggers     | string?                                 | Where, when, or under what conditions the problem appears.                                                                                              |
| current_workarounds      | string?                                 | How users currently cope with the problem (manual hacks, spreadsheets, etc.).                                                                           |
| qualitative_pain_summary | string?                                 | Text summary of how painful this problem is, based on user statements.                                                                                  |
| quantified_impact        | QuantifiedImpact?                       | Structured time/cost/frequency estimates if available.                                                                                                  |
| evidence_sources         | list[string]?                           | High-level list of evidence sources (e.g. “3 interviews”, “1 internal report”).                                                                         |
| indicators               | dict[VRCIndicatorId, IndicatorEvidence] | Evidence snapshot for VRC indicators 1–5 (problem clarity, severity, market size validation, target customer ID, pain quantification).[attached_file:1] |
| author                   | string?                                 | Optional author or owner of this artifact.                                                                                                              |
| notes                    | string?                                 | Free-form notes or comments.                                                                                                                            |

### CustomerSegment

| Field              | Type    | Description                                                     |
| ------------------ | ------- | --------------------------------------------------------------- |
| label              | string  | High-level name of the segment, e.g. “DTC subscription brands”. |
| role               | string? | Typical role, e.g. “Founder”, “Head of Operations”.             |
| company_size_range | string? | Company size band, e.g. “50–200 employees”.                     |
| revenue_range      | string? | Revenue band, e.g. “$50k–$500k MRR”.                            |
| geography          | string? | Geographic focus if relevant.                                   |

### QuantifiedImpact

| Field                               | Type    | Description                                                    |
| ----------------------------------- | ------- | -------------------------------------------------------------- |
| time_lost_per_user_per_period_hours | float?  | Estimated time lost per user per period (e.g. hours per week). |
| cost_impact_per_user_per_period     | float?  | Estimated financial impact per user per period.                |
| currency                            | string? | Currency for the cost impact (e.g. “USD”).                     |
| frequency                           | string? | Frequency label, e.g. “daily”, “weekly”, “monthly”.            |
| notes                               | string? | Assumptions or caveats about these estimates.                  |

### IndicatorEvidence

| Field          | Type           | Description                                                                                 |
| -------------- | -------------- | ------------------------------------------------------------------------------------------- |
| indicator_id   | VRCIndicatorId | Which VRC indicator this evidence refers to (I1–I5 here).                                   |
| tier           | EvidenceTier   | Current evidence tier (E0–E4) for this indicator.[attached_file:1]                          |
| score_0_to_100 | int            | Current scored value (0–100) for this indicator.                                            |
| notes          | string?        | Why this tier/score was chosen (what evidence was used).                                    |
| next_steps     | string?        | Concrete recommendation on how to improve this indicator (what to gather or validate next). |
