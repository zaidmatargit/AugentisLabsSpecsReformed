# VRCAssessmentArtifact

Represents the full 20‑indicator VRC result, the gate decision, and a prioritized improvement plan for moving from Discover to Define. This artifact is produced by the VRC Assessor and consumes the Discover artifacts (Problem Statement, Competitive Landscape, Market Opportunity).[attached_file:1]

## Pydantic model

from enum import Enum
from typing import List, Optional
from datetime import datetime
from pydantic import BaseModel, Field

class EvidenceTier(str, Enum):
E0 = "E0"
E1 = "E1"
E2 = "E2"
E3 = "E3"
E4 = "E4"

class GateDecision(str, Enum):
PASS = "pass"
CONDITIONAL_PASS = "conditional_pass"
BLOCK = "block"

class Pillar(str, Enum):
PROBLEM_UNDERSTANDING = "problem_understanding"
MARKET_INTELLIGENCE = "market_intelligence"
SOLUTION_VALIDATION = "solution_validation"
TEAM_RESOURCES = "team_resources"

class VRCIndicatorResult(BaseModel):
indicator_id: str
name: str
pillar: Pillar
tier: EvidenceTier
score_0_to_100: int = Field(ge=0, le=100)
rationale: Optional[str] = None
next_steps: Optional[str] = None

class ImprovementAction(BaseModel):
title: str
description: str
related_indicators: List[str]
priority: int = Field(ge=1, le=5)
estimated_effort: Optional[str] = None
owner: Optional[str] = None

class VRCAssessmentArtifact(BaseModel):
artifact_id: str
version: str = "0.1"
created_at: datetime
updated_at: datetime

problem_artifact_id: str
competitive_artifact_id: Optional[str] = None
market_artifact_id: Optional[str] = None

overall_score_0_to_100: int = Field(ge=0, le=100)
gate_decision: GateDecision

strengths: List[str]
risks: List[str]

indicators: List[VRCIndicatorResult]

improvement_plan: List[ImprovementAction]

comparison_to_previous: Optional[str] = None
notes: Optional[str] = None

## Field descriptions

### VRCIndicatorResult

| Field          | Type         | Description                                                                                                                                                    |
| -------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| indicator_id   | string       | Unique ID of the indicator, e.g. `"I1_problem_clarity"`, matching your 20-indicator VRC framework.[attached_file:1]                                            |
| name           | string       | Human‑readable label of the indicator, e.g. `"Problem Clarity and Definition"`.                                                                                |
| pillar         | Pillar       | Which readiness pillar this indicator belongs to: `problem_understanding`, `market_intelligence`, `solution_validation`, or `team_resources`.[attached_file:1] |
| tier           | EvidenceTier | Current evidence tier (E0–E4) representing how strong the supporting data is for this indicator.                                                               |
| score_0_to_100 | int          | Numeric score for the indicator (0–100) after applying the rubric and evidence tier logic.                                                                     |
| rationale      | string?      | Short explanation of why this indicator received its tier and score (which evidence was considered).                                                           |
| next_steps     | string?      | Concrete recommendation for improving this indicator (what to do next to move it to a higher tier/score).                                                      |

### VRCAssessmentArtifact

| Field                   | Type                     | Description                                                                                                           |
| ----------------------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------- |
| artifact_id             | string                   | Unique ID for this VRC assessment document.                                                                           |
| version                 | string                   | Version label, e.g. `"0.1"`, allowing progressive updates without changing the schema.                                |
| created_at              | datetime                 | Timestamp when this VRC assessment was first created.                                                                 |
| updated_at              | datetime                 | Timestamp of the last modification to this assessment.                                                                |
| problem_artifact_id     | string                   | ID of the ProblemStatementArtifact this assessment is based on.                                                       |
| competitive_artifact_id | string?                  | ID of the CompetitiveLandscapeArtifact used (may be `None` if not yet available).                                     |
| market_artifact_id      | string?                  | ID of the MarketOpportunityArtifact used (may be `None` if not yet available).                                        |
| overall_score_0_to_100  | int                      | Aggregated VRC score across all 20 indicators (0–100) using your weighting logic and evidence tiers.[attached_file:1] |
| gate_decision           | GateDecision             | Phase gate recommendation: `pass` (≥70), `conditional_pass` (60–69), or `block` (<60).                                |
| strengths               | list[string]             | Bullet-style descriptions of the strongest aspects of the venture (highest scoring indicators/pillars).               |
| risks                   | list[string]             | Bullet-style descriptions of key risks or weakest indicators that may block progression.                              |
| indicators              | list[VRCIndicatorResult] | List of all 20 indicator results, one per VRC indicator, with pillar, tier, score, rationale, and next steps.         |
| improvement_plan        | list[ImprovementAction]  | Concrete backlog of actions that should improve specific indicators and raise the VRC score.                          |
| comparison_to_previous  | string?                  | Optional short narrative comparing this assessment to the prior one (what improved, what regressed).                  |
| notes                   | string?                  | Free-form comments for anything that doesn’t fit into the structured fields.                                          |
