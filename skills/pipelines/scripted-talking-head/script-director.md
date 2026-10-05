# Script Director — Scripted Talking-Head Pipeline

## When to Use

You have a brief (with a `shooting_script.md` reference) and now have the actual recorded
footage. Your job is the same core task as `talking-head`'s script stage: transcribe the
real footage and structure it into the canonical `script` artifact — **plus** reconcile the
transcript against the shooting script, so any gap between what was planned and what was
actually performed is visible to downstream stages instead of silently lost.

## Prerequisites

| Layer | Resource | Purpose |
|-------|----------|---------|
| Schema | `schemas/artifacts/script.schema.json` | Artifact validation |
| Prior artifacts | `state.artifacts["idea"]["brief"]` | Content context, `metadata.shooting_script_path` |
| Tools | `transcriber` (WhisperX) | Speech-to-text with timestamps |

## Process

### Step 1: Transcribe

Use the transcriber tool to get word-level timestamps:
- Model: `large-v3` for best quality, `base` for speed
- Enable word-level alignment for precise timing
- Note language detection result

### Step 2: Segment into Sections

Group the transcript into logical sections:
- Detect topic changes by content
- Respect natural pauses (> 1.5s silence = potential section break)
- Each section gets: id, text, start_seconds, end_seconds

### Step 3: Enhance Section Metadata

For each section, add:
- Enhancement cues (where overlays, b-roll, or text cards could go)
- Speaker notes (emphasis, pace changes detected in audio)

### Step 3b: Reconcile Against the Shooting Script (unique to this pipeline)

Load `shooting_script.md` from the brief's `metadata.shooting_script_path`. For each beat
in the shooting script, find its match in the transcript and classify it:

| Classification | Meaning |
|---|---|
| `as_written` | Performed close to word-for-word |
| `paraphrased` | Same intent, different wording |
| `materially_different` | Content/claim changed in a way that matters |
| `cut` | Planned beat doesn't appear in the recording at all |
| `improvised` | Transcript contains content with no counterpart in the shooting script |

**The transcript is the source of truth.** Never silently prefer the plan over what was
actually said — if they diverge, the real recording wins. The shooting script only exists
to explain *why* a line reads a certain way and to catch beats that got dropped
unintentionally.

Log every `materially_different`, `cut`, or `improvised` case in the decision log:
`category: "script_reconciliation"`, noting which beat, what changed, and whether it looks
intentional (a better ad-lib) or accidental (a dropped line worth flagging to the user).

### Step 4: Build Script Artifact

Assemble the structured script per `schemas/artifacts/script.schema.json`:
- Total duration (from transcript)
- All sections with timestamps
- Enhancement cues per section
- `metadata.reconciliation`: the beat-by-beat classification from Step 3b

### Step 5: Self-Evaluate

| Criterion | Question |
|-----------|----------|
| **Transcription accuracy** | Are the words correct? (Spot-check a few sections) |
| **Timestamp accuracy** | Do section boundaries align with actual speech? |
| **Coverage** | Does the script span the full footage duration? |
| **Reconciliation** | Is every `materially_different`/`cut`/`improvised` beat logged, not silently absorbed? |

### Step 6: Submit

Validate the script against the schema and persist via checkpoint.

### Mid-Production Fact Verification

If you encounter uncertainty during script writing:
- Use `web_search` to verify factual claims before committing them to the script
- Use `web_search` to find reference images for visual accuracy
- Log verification in the decision log: `category="visual_accuracy_check"`

Every factual claim in the script should be traceable to the brief or the actual
transcript. Do not invent statistics, dates, or attributions.

---

## Gate Reminder (Binding)

This stage gates on human approval (`human_approval_default: true`). After review passes:
checkpoint with `status="awaiting_human"`, present the summary (the Backlot board renders
the artifact), and **END YOUR TURN**. Do not start the next stage in the same response.
Approval is per-gate — an earlier "go ahead" does not cover this gate.
