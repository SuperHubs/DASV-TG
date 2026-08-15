# Candidate Generation Prompts


This file provides the fixed route-specific prompts used for candidate
interval generation in DASV-TG.

According to the difficulty-aware routing mechanism, different candidate
sources are activated for different reasoning paths:

- R_text activates H_text.
- R_multi activates H_text, H_visual, and H_fused.
- R_complex activates H_text, H_visual, H_fused, and H_proc.

Each activated candidate source is generated using a fixed prompt during
inference. Each prompt returns multiple candidate intervals for subsequent
route-specific verification.

The structured question clues extracted from the question are used to
guide candidate generation.

---

## 1. Text-based Candidate Generation (H_text)

You are an expert in temporal grounding for medical instructional videos.

Given a question, structured question clues, and timestamped textual
evidence, identify relevant temporal intervals that contain sufficient
textual evidence to answer the question.

Use the structured question clues to identify relevant textual spans.

Focus on:
- question-related keywords,
- semantic matching between the question and textual evidence,
- temporal continuity of relevant evidence segments.

Avoid unrelated segments.

Return at most three candidate intervals in descending order of relevance.

Output only JSON:

{
  "candidates": [
    {
      "start_time": float,
      "end_time": float
    }
  ]
}

Question:
{QUESTION}

Structured Question Clues:
{STRUCTURED_CLUES}

Textual Evidence:
{TEXT_EVIDENCE}

---

## 2. Visual Candidate Generation (H_visual)

You are an expert in visual temporal grounding for medical instructional
videos.

Given a question, structured question clues, and timestamped visual
evidence, identify temporal intervals where the required visual
information is demonstrated.

Use the structured question clues to locate relevant visual evidence.

Focus on:
- visible actions,
- body parts,
- instruments,
- patient posture,
- operation direction,
- operation states.

Focus on operation-related visual evidence and ignore irrelevant
background information.

Return at most three candidate intervals in descending order of relevance.

Output only JSON:

{
  "candidates": [
    {
      "start_time": float,
      "end_time": float
    }
  ]
}

Question:
{QUESTION}

Structured Question Clues:
{STRUCTURED_CLUES}

Visual Evidence:
{VISUAL_EVIDENCE}

---

## 3. Bimodal Fusion Candidate Generation (H_fused)

You are an expert in multimodal temporal answer grounding.

Given a question, structured question clues, timestamped textual evidence,
and visual evidence, generate temporal intervals supported by consistent
textual and visual information.

Use the structured question clues to align textual and visual evidence.

Consider:
- subtitle-visual alignment,
- cross-modal consistency,
- temporal correspondence between textual and visualevidence, allowing for short speech--action delays,
- relevant action occurrence.

Return at most three candidate intervals in descending order of relevance.

Output only JSON:

{
  "candidates": [
    {
      "start_time": float,
      "end_time": float
    }
  ]
}

Question:
{QUESTION}

Structured Question Clues:
{STRUCTURED_CLUES}

Textual Evidence:
{TEXT_EVIDENCE}

Visual Evidence:
{VISUAL_EVIDENCE}

---

## 4. Procedure-aware Candidate Generation (H_proc)

You are an expert in medical procedural video understanding.

Given a question, structured question clues, and multimodal evidence,
identify temporal intervals covering the complete procedural step required
to answer the question.

Use the structured question clues to identify the target procedural step.

Consider:
- action decomposition,
- procedural order,
- workflow continuity,
- relationships between prerequisite and subsequent steps.

Return at most three candidate intervals in descending order of relevance.

Output only JSON:

{
  "candidates": [
    {
      "start_time": float,
      "end_time": float
    }
  ]
}

Question:
{QUESTION}

Structured Question Clues:
{STRUCTURED_CLUES}

Multimodal Evidence:
{MULTIMODAL_EVIDENCE}
