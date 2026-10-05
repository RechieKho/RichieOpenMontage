# Idea Director — Scripted Talking-Head Pipeline

## When to Use

You are starting an **idea-first** talking-head video: you have the user's high-level
context (topic, goal, tone/style, platform, duration) but no footage yet. The user will
record the footage themselves afterward — through a camera, webcam, or software like a
PNGtuber rig — to perform a script you help write now. This pipeline exists specifically
for that inverted order: `talking-head`'s `idea` stage assumes footage already exists and
extracts a brief from it; this stage authors the brief from intent and hands the user
something to record against.

## Relationship to `talking-head`

Everything downstream (`script` → `scene_plan` → `assets` → `edit` → `compose` →
`publish`) is shared — same stage skills, same artifact schemas. Footage still gets
transcribed for real once it exists; nothing downstream trusts this stage's script as
final. The only structural difference is here: the brief comes from the user's stated
intent, not from watching existing material, and this stage additionally produces a
shooting script.

## Runtime Selection (MANDATORY — present the constraint, don't silently pick)

Lock `render_runtime = "remotion"` (preferred — uses `TalkingHead` + `remotion_caption_burn`)
or `"ffmpeg"` (for source-footage concat with no composition). HyperFrames is not a valid
runtime on this pipeline family — the TalkingHead composition and word-level caption burn
have no HyperFrames parity. Per AGENT_GUIDE.md → "Present Both Composition Runtimes (HARD
RULE)": tell the user HyperFrames exists but isn't viable here, and log the rejection in
`render_runtime_selection`.

## Process

### Step 1: Check for an Applicable Profile

Before asking the user to re-derive anything, check `profiles/` for one that already fits
this request (pipeline, tone, asset conventions). Reuse it rather than re-deriving — see
`profiles/PROFILE.md`.

### Step 2: Gather Context

If not already provided, ask for:
- Topic/subject and the concrete goal (awareness, conversion/CTA, education, entertainment)
- Target platform and duration
- Tone/style — a direct description or reference creators/videos
- Any constraints on the recording (e.g. PNGtuber software with lip-sync baked in, webcam,
  green screen)

### Step 3: Draft the Brief

Build the brief artifact per `schemas/artifacts/brief.schema.json` — same required fields
as any `idea` stage (`title`, `hook`, `key_points`, `tone`, `style`, `target_platform`,
`target_duration_seconds`; `cta` when there's a conversion goal). Since footage doesn't
exist yet:
- Do not set `reference_material` to a footage path — leave it empty or populated with
  actual reference videos/channels the user named.
- Set `metadata.footage_status: "pending"`.
- Set `metadata.shooting_script_path` once Step 4 is done.

### Step 4: Draft the Shooting Script (this stage's unique output)

Write a beat-by-beat script the user can perform on camera. This is **not** the canonical
`script` artifact — that only exists once real footage is transcribed. It's a recording
aid. Save it to `<project>/artifacts/shooting_script.md` and reference its path in the
brief's `metadata`.

Structure it as discrete beats (hook / context / payoff / CTA, or whatever the content
calls for), each readable as its own take — this is what makes jump-cut editing clean
later. Flag every placeholder you invent (made-up numbers, timelines, specifics) so the
user knows to swap in the real thing before recording.

### Step 5: Draft Supplementary Asset Plan (optional)

If the video wants visual variety beyond straight talking-head footage, segregate planned
cutaways immediately:

- **Capturable** — anything with something real to point a recorder at (actual
  product/gameplay footage, real UI/software, a live page) — record it, don't draw/generate it
- **Illustrated/generated** — conceptual beats with nothing to literally capture

Save this as `<project>/artifacts/supplementary_assets.md` if produced. This isn't
required at this stage — only draft it if the enhancement direction is already clear.

### Step 6: Self-Evaluate

| Criterion | Question |
|-----------|----------|
| **Captures intent** | Does the brief reflect what the user actually wants, not a guess? |
| **Performable** | Could the user realistically read/perform the shooting script as written? |
| **Platform fit** | Is the target platform/duration realistic for this content? |
| **Footage status explicit** | Does the brief clearly flag footage as pending rather than assuming it exists? |

### Step 7: Submit

Validate the brief against the schema, write the shooting script (and supplementary asset
plan, if produced) to disk, and checkpoint.

## When Footage Arrives

Hand off to the `script` stage. It transcribes the real recording and reconciles it
against `shooting_script.md` — the performed take is the source of truth; the shooting
script is context for why a line reads the way it does, never an override.

---

## Gate Reminder (Binding)

This stage gates on human approval (`human_approval_default: true`). After review passes:
checkpoint with `status="awaiting_human"`, present the summary (the Backlot board renders
the artifact), and **END YOUR TURN**. Do not start the next stage in the same response.
Approval is per-gate — an earlier "go ahead" does not cover this gate.
