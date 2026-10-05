# Profiles

A **profile** is a condensed, reusable production recipe: a pipeline choice plus
the style, tone, and asset-sourcing conventions that go with it, captured once so
a recurring kind of video doesn't need its decisions re-derived from scratch
every time.

Profiles are not a new pipeline layer and they don't replace anything in
`pipeline_defs/`, `styles/`, or `skills/`. A profile just points at which
pipeline to use, which style playbook to lean on (or override), and the
project-specific conventions that came out of a real planning conversation —
things like "which overlays should be screen-recorded vs hand-drawn" or
"this channel's comedic register." Read the linked pipeline manifest and
stage director skills as the actual authority; the profile is the shortcut
back to the decisions already made for this kind of video.

**When to use one:** at the `idea`/proposal stage, before running preflight,
if the user's request matches an existing profile — reuse its pipeline,
tone, and asset conventions instead of re-asking the same scoping questions.

**When to add one:** after a planning conversation establishes a repeatable
pattern (recurring show, channel, or series) worth not re-deriving next time.

## Available Profiles

| Profile | Pipeline | Description |
|---|---|---|
| [`talking_png_head`](talking_png_head.md) | `talking-head` | Casual/funny PNGtuber-style talking-head videos (pre-synced avatar recording) with hand-drawn interstitial illustrations and screen-recorded gameplay/UI cutaways. |
