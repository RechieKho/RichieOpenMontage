# Profile: talking_png_head

Casual, funny, PNGtuber-style talking-head videos — a drawn avatar delivering
narration, recorded directly from PNGtuber software (lip-sync already baked
into the footage), enhanced with hand-drawn interstitial illustrations and
real screen-recorded gameplay/UI cutaways.

## Pipeline

`scripted-talking-head` (`pipeline_defs/scripted-talking-head.yaml`) — the
idea-first variant of `talking-head`. This profile's whole premise is
write-script-then-perform-it, which is exactly what that pipeline's `idea`
stage is for: it drafts the brief and a shooting script from context before
any footage exists, then the `script` stage transcribes the real PNGtuber
recording and reconciles it against the plan once it's delivered.

The PNGtuber software's recorded output is the raw footage input for the
`script` stage onward — it is already a video file with correct mouth/audio
alignment, so it enters the pipeline exactly like a camera recording would.
No `character-animation` rigging is needed; that pipeline only applies if
the character needs to be rigged and driven by this system instead of the
user's own PNGtuber tool.

If the user instead already has raw footage in hand with nothing pre-written
(a true footage-first case), fall back to plain `talking-head` — this
profile's asset-sourcing and tone guidance still apply either way.

`render_runtime`: `remotion` (TalkingHead composition + `remotion_caption_burn`).
HyperFrames is not viable on this pipeline (no TalkingHead/caption parity yet)
— still present it as the rejected alternative per the composition-runtime
hard rule, don't skip announcing it.

## Tone & Style Reference

Casual, funny, self-deprecating, direct-to-camera. Fast jump cuts between
beats rather than one continuous take. Reference channels: Ringo Tsuga,
Jaiden Animations — expressive delivery, personal/indie-dev voice, humor
over polish.

Closest style playbook: `flat-motion-graphics` (bold, punchy, social-native).
None of the existing playbooks are a perfect match for this register —
treat it as a starting point and let the channel's actual comedic voice
override it (`custom_allowed: true` on the pipeline).

## Format Defaults

- Vertical, short-form (under 60s)
- Hook in the first 3-4 seconds
- Script written as discrete beats (hook / context / payoff / CTA), each
  readable as its own recorded jump-cut segment

## Asset Conventions (the load-bearing part of this profile)

Every supplementary visual gets sorted into one of two buckets before
drawing anything — this is the single biggest time-saver this profile
encodes:

**Screen-record, don't draw:**
- Real gameplay footage (actual play, not an illustrated mock of it) —
  authenticity beats cuteness here; this is proof the thing exists
- Existing in-game UI (title screens, menus) if already built
- A literal rage-quit / force-close of the game window, if that beat exists
  in the script, instead of illustrating the reaction
- Anything that will later go live (e.g. a storefront page) — illustrate a
  mock version now, swap in the real screen recording once it exists

**Draw (nothing to point a recorder at):**
- Conceptual/joke illustrations (e.g. a messy desk, an exaggerated
  reaction) that depict something true in spirit, not a literal place or
  screen
- Branding elements without an existing in-product equivalent (logo card,
  CTA card) until the real thing ships

**Overlay execution:**
- Keep drawn overlays in the same palette/line-weight as the PNGtuber
  character so they read as one world, not stock art bolted on
- Overlay density: 3-6 per minute per the scene-director guideline, scaled
  down proportionally for anything under a minute (aim 3-4 total, not 5+)
- Default overlay rig per PNGtuber character (see `rig_plan` conventions in
  `skills/creative/character-rigging` if ever rigged natively instead of
  recorded): body + head base (static), mouth (closed/open), eyes
  (open/blink), eyebrows (neutral/raised/flat) — only relevant if this
  profile is ever adapted to native rigging instead of a PNGtuber-software
  recording

## Primary Goal Pattern

These videos typically exist to drive a specific conversion action (e.g.
wishlist a Steam page, subscribe, follow a devlog) rather than pure
entertainment — the CTA beat should be concrete and placed late but not
buried (last 15-20% of runtime), and the asset-conventions table above
should earmark one overlay specifically for that CTA.
