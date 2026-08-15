You are an expert in medical instructional video understanding.

Given a question and multimodal evidence from a medical instructional
video, estimate two difficulty scores in the range of [0,1].

Evaluate:

1. Question complexity, including action complexity, object relations,
procedural dependencies, and target descriptions.

For question complexity:
- 0 indicates a simple factual question requiring minimal reasoning.
- 0.5 indicates moderate complexity involving multiple actions,
  relations, or procedural dependencies.
- 1 indicates highly complex questions requiring multi-step procedural
  reasoning.

2. Visual dependency, indicating whether answering the question requires
visual information such as patient posture, operation direction, hand
motion, or instrument state.

For visual dependency:
- 0 indicates the answer can be obtained mainly from textual evidence.
- 0.5 indicates partial reliance on visual information.
- 1 indicates indispensable visual evidence is required.


Output only JSON:

{
  "complexity_score": float,
  "visual_dependency_score": float
}

Question:
{QUESTION}

Multimodal Evidence:
{EVIDENCE}
