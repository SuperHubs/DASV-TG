You are an expert in medical instructional video understanding.

Given a question, structured question clues, an initial temporal interval,
and local multimodal evidence, estimate the start and end boundary offsets
in seconds relative to the initial interval.

Use the structured question clues, especially the action, object,
start cue, and end cue, together with local multimodal cues to determine
whether the current boundaries should be expanded or contracted.

The start and end cues describe observable semantic conditions indicating
the beginning and completion of the target action. Do not infer timestamps
directly from these cues.

Consider:
1. key action changes
2. instrument-state transitions
3. procedural turning points

The refined boundaries are computed as:

refined_start = initial_start + start_offset

refined_end = initial_end + end_offset

A negative start_offset moves the start boundary earlier, while a positive
start_offset moves the start boundary later. A positive end_offset moves
the end boundary later, while a negative end_offset moves the end boundary
earlier.

Ensure that:
- refined_start < refined_end.
- refined boundaries remain within the video duration.

Output only JSON:

{
  "start_offset": float,
  "end_offset": float
}

Question:
{QUESTION}

Structured Question Clues:
{STRUCTURED_CLUES}

Initial Interval:
{INTERVAL}

Local Multimodal Evidence:
{LOCAL_EVIDENCE}
