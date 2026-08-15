# Inference Settings

## Model Versions

- GPT verifier: `gpt-4o-2024-11-20`.
- Qwen backbone: `Qwen/Qwen3.5-35B-A3B`.

## Decoding Parameters

- Local Qwen: sampling and thinking disabled; `temperature=0` through the
  OpenAI-compatible SGLang endpoint.
- GPT-4o verifier: `temperature=0`, `top_p=1.0`.
- Global preprocessing and runtime seed: `2026`.

| Task | Max output tokens |
|---|---:|
| Visual evidence construction | 1024 |
| Difficulty estimation | 128 |
| Question clue extraction | 256 |
| Candidate generation | 512 |
| Candidate verification | 256 |
| Boundary refinement | 128 |

## Frame Sampling and Segmentation

- Global sampling: 2 fps; non-overlapping 2-second visual units; four frames
  and one structured description with start and end timestamps per unit.
- Visual-evidence batch: at most eight units (32 frames) per request.
- Boundary refinement: 4 fps within the local window.

## Text Encoder

`BAAI/bge-m3` with a fixed commit SHA; L2-normalized 1024-dimensional dense
embeddings and cosine similarity.

## JSON and Invalid-Offset Handling

```python
import json
import math
import re


MAX_JSON_RETRIES = 1
MAX_BOUNDARY_OFFSET_SECONDS = 10.0
MIN_REFINED_INTERVAL_SECONDS = 0.5
TIMESTAMP_DECIMAL_PLACES = 1
MARKDOWN_FENCE = "`" * 3
JSON_REPAIR_SUFFIX = (
    "\nThe previous response was invalid. Return exactly one valid JSON "
    "object with all required fields and no explanatory text."
)


def _strip_json_fence(raw_text):
    """Remove one complete Markdown JSON fence, if present."""
    if not isinstance(raw_text, str):
        raise ValueError("The response must be a string.")

    text = raw_text.strip()
    if not text:
        raise ValueError("The response is empty.")

    if text.startswith(MARKDOWN_FENCE):
        match = re.fullmatch(
            re.escape(MARKDOWN_FENCE)
            + r"[ \t]*(?:json)?[ \t]*(?:\r?\n)?(?P<body>.*?)"
            + re.escape(MARKDOWN_FENCE),
            text,
            flags=re.IGNORECASE | re.DOTALL,
        )
        if match is None:
            raise ValueError("Malformed or unsupported Markdown code fence.")
        text = match.group("body").strip()

    if not text:
        raise ValueError("The JSON content is empty.")

    return text


def parse_json_response(raw_text, required_fields):
    """Parse and validate an LLM JSON response."""
    text = _strip_json_fence(raw_text)
    try:
        data = json.loads(text)
    except (json.JSONDecodeError, TypeError) as exc:
        raise ValueError(f"Invalid JSON response: {exc}") from exc

    if not isinstance(data, dict):
        raise ValueError("The response must be a JSON object.")

    missing = [field for field in required_fields if field not in data]
    if missing:
        raise ValueError(f"Missing required fields: {missing}")

    return data


def request_and_parse_json(request_fn, required_fields):
    """Request, validate, and retry once with a fixed repair instruction."""
    last_error = None
    for attempt in range(MAX_JSON_RETRIES + 1):
        repair_suffix = "" if attempt == 0 else JSON_REPAIR_SUFFIX
        raw_text = request_fn(repair_suffix)
        try:
            return parse_json_response(raw_text, required_fields)
        except ValueError as exc:
            last_error = exc

    raise ValueError(
        f"No valid JSON response after {MAX_JSON_RETRIES + 1} attempts: "
        f"{last_error}"
    ) from last_error


def _is_finite_number(value):
    return (
        isinstance(value, (int, float))
        and not isinstance(value, bool)
        and math.isfinite(value)
    )


def apply_boundary_offsets(
    initial_start,
    initial_end,
    start_offset,
    end_offset,
    video_duration,
):
    """Apply validated offsets; return the initial interval on failure."""
    initial_values = [initial_start, initial_end, video_duration]
    if not all(_is_finite_number(x) for x in initial_values):
        raise ValueError("Initial interval and video duration must be finite.")
    if not (0.0 <= initial_start < initial_end <= video_duration):
        raise ValueError("The initial interval is invalid or reversed.")

    initial = (initial_start, initial_end)
    if not all(_is_finite_number(x) for x in [start_offset, end_offset]):
        return initial

    limit = MAX_BOUNDARY_OFFSET_SECONDS
    start_offset = max(-limit, min(limit, start_offset))
    end_offset = max(-limit, min(limit, end_offset))

    refined_start = max(
        0.0,
        min(video_duration, initial_start + start_offset),
    )
    refined_end = max(
        0.0,
        min(video_duration, initial_end + end_offset),
    )

    if refined_start >= refined_end:
        return initial
    if refined_end - refined_start < MIN_REFINED_INTERVAL_SECONDS:
        return initial

    return (
        round(refined_start, TIMESTAMP_DECIMAL_PLACES),
        round(refined_end, TIMESTAMP_DECIMAL_PLACES),
    )
```

Invalid JSON is retried once with the fixed repair suffix and otherwise
discarded. Invalid refined boundaries retain the initial interval; an invalid
initial interval raises an upstream-data exception.
