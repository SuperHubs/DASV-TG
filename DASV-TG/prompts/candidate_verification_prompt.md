You are an expert in medical instructional video understanding.

Given a question, a candidate temporal segment, and the available
multimodal evidence, evaluate whether the candidate contains sufficient
evidence required to answer the question.

Assess the following aspects when supported by the provided evidence:

1. textual consistency
2. visual consistency
3. procedural consistency
4. answer completeness

Only evaluate the dimensions supported by the provided evidence.
For unavailable evidence types, return null and do not assign a score.

Assign each available aspect a score in [0,1], where:
- 0 indicates no supporting evidence or contradictory evidence.
- 1 indicates complete and consistent support.

Base the scores only on the provided question, candidate interval,
and available multimodal evidence.

Output only JSON:

{
  "text_score": float or null,
  "visual_score": float or null,
  "procedural_score": float or null,
  "answer_completeness_score": float
}

Question:
{QUESTION}

Candidate:
{CANDIDATE}

Available Multimodal Evidence:
{CANDIDATE_EVIDENCE}
