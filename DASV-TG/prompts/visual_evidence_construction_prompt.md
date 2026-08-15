You are an expert in medical instructional video understanding.

Given sampled frames from a temporal segment of a medical instructional
video, generate a structured visual description for temporal grounding.

For each segment, identify:

1. action
2. body part
3. instrument
4. posture
5. operation direction
6. operation state

Focus on operation-related information and ignore irrelevant background
details.

Output only JSON:

{
  "segments": [
    {
      "start_time": float,
      "end_time": float,
      "action": str,
      "body_part": str,
      "instrument": str,
      "posture": str,
      "operation_direction": str,
      "operation_state": str
    }
  ]
}

Segment Frames:
{FRAMES}
