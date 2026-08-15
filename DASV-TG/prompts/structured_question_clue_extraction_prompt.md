You are an expert in medical instructional video understanding.

Given a natural-language question from a medical instructional video, extract
structured temporal grounding clues.

Identify:
1. question intent
2. core action
3. operation object
4. textual clue
5. visual clue
6. start cue
7. end cue

The extracted clues should support textual retrieval,
visual evidence retrieval, and temporal boundary localization.

Output only JSON:

{
  "intent": str,
  "action": str,
  "object": str,
  "text_clue": str,
  "visual_clue": str,
  "start_cue": str,
  "end_cue": str
}

Question:
{QUESTION}
