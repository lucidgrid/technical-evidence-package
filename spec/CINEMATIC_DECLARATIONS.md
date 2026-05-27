# Cinematic Declarations

**Version:** 1.0.0 (schema v1.9.0)

The cinematic declaration is a per-asset record of the framing and cinematography decisions made by the human directing an AI-generated video or image asset. It is activated for video and image assets when the user declares cinematic parameters during the attestation workflow.

The cinematic declaration is the evidence record for shot-level human creative direction in AI-generated content. It is what converts a creative intent into a documentable authorship claim.

---

## When to use

The cinematic declaration applies to:
- AI-generated video assets (Runway, Luma, Kling, Sora, Higgsfield, Wan, CogVideoX, HunyuanVideo, Seedance, Pika)
- AI-generated image assets where shot composition and framing were explicitly directed
- AI-generated image-to-3D assets where the reference image carried framing intent (Meshy, TripoAI, Kaedim)

It does not apply to:
- Audio assets (ElevenLabs, Suno, Udio) — use the standard HITL taxonomy
- Text-only prompts with no visual output
- Automated pipeline outputs where no human framing intent was declared

---

## Field reference

### Required when cinematic declaration is activated

| Field | Type | Description |
|---|---|---|
| `shot_type` | string | The framing declaration for the shot |
| `frame_rate` | string | Target frame rate declared by the artist |
| `parameter_origin` | string enum | Who decided the cinematic parameters |

### Optional fields

| Field | Type | Description |
|---|---|---|
| `camera_movement` | string | Declared camera motion intent |
| `focal_length_range` | string | Artist's declared focal length intent |
| `depth_of_field` | string | Artist's declared depth of field intent |
| `aspect_ratio` | string | Declared aspect ratio |
| `color_space` | string | Declared color space / color science intent |
| `dynamic_range` | string | HDR / SDR intent |
| `lighting_intent` | string | Free-text lighting direction |
| `cinematic_intent` | string | Free-text overall creative direction |
| `prompt_excerpt` | string | The prompt text that operationalized the declared framing |
| `asc_fdl_link` | string (URN) | Reference to physical production's ASC FDL canvas |
| `declared_by` | string | Name of the human making the declaration |
| `declared_at` | ISO-8601 UTC | Timestamp of the declaration |

---

## Enumerated values

### `shot_type`
`wide` | `medium_wide` | `medium` | `medium_close` | `close` | `extreme_close` | `insert` | `aerial` | `pov`

Aligned to ASC FDL `framing_intent` canvas type vocabulary.

### `camera_movement`
`static` | `dolly_in` | `dolly_out` | `dolly_lateral` | `pan_left` | `pan_right` | `tilt_up` | `tilt_down` | `crane_up` | `crane_down` | `handheld` | `zoom_in` | `zoom_out` | `orbit` | `tracking`

Aligned to OpenTrackIO `transforms[].rotation` motion vector vocabulary.

### `focal_length_range`
`ultra_wide` | `wide` | `normal` | `telephoto` | `extreme_telephoto`

Artist's declared intent, not a physical measurement. OpenTrackIO records the physical focal length in mm. This field records the artist's compositional intent.

### `depth_of_field`
`shallow` | `medium` | `deep`

### `parameter_origin`

| Value | Meaning |
|---|---|
| `human_specified` | The artist explicitly set each parameter using tool controls |
| `human_directed_ai_executed` | The artist declared intent in natural language; the AI model interpreted and executed it |
| `ai_generated` | No human framing direction was provided; the AI model determined framing autonomously |
| `mixed` | Some parameters were human-specified, others AI-determined |

**`parameter_origin` is the architectural argument.** This field has no equivalent in OpenTrackIO, the ASC FDL, or any physical production standard — because physical production never needed it. A camera does not choose its own focal length. `parameter_origin` is the field that converts a framing declaration from a production note into an authorship record. It documents whether a human made the decision or whether the AI did.

---

## Example: hybrid production

An AI-generated wide shot composited with physical plates from an on-set ASC FDL record:

```json
{
  "cinematic_declaration": {
    "shot_type": "wide",
    "frame_rate": "23.976",
    "aspect_ratio": "2.39:1",
    "camera_movement": "static",
    "focal_length_range": "wide",
    "depth_of_field": "deep",
    "color_space": "ACES AP0",
    "dynamic_range": "HDR",
    "lighting_intent": "overcast exterior, diffuse, no hard shadows",
    "parameter_origin": "human_directed_ai_executed",
    "cinematic_intent": "Match the establishing wide from the physical plate shoot. Overcast Icelandic exterior feel. The AI-generated background extends the location.",
    "prompt_excerpt": "wide establishing shot, overcast exterior, diffuse lighting, deep focus, 2.39 aspect",
    "asc_fdl_link": "urn:asc:fdl:AGM_104:canvas_exterior_001",
    "declared_by": "Jordan Reyes, VFX Supervisor",
    "declared_at": "2026-05-15T14:22:00Z"
  }
}
```

`asc_fdl_link` connects this AI authorship declaration to the physical production's FDL canvas. A pipeline supervisor can cross-reference the framing intent against the on-set data without requiring LucidGrid access.

---

## Example: AI-only production

A Runway Gen-4 video with fully human-directed framing:

```json
{
  "cinematic_declaration": {
    "shot_type": "medium",
    "frame_rate": "24",
    "camera_movement": "dolly_in",
    "focal_length_range": "telephoto",
    "depth_of_field": "shallow",
    "parameter_origin": "human_specified",
    "cinematic_intent": "Slow push on the subject as the decision lands. Telephoto compression. Shallow focus isolates the face from the background tension.",
    "prompt_excerpt": "medium shot dolly push in, telephoto, shallow focus, motivated by decision moment, clinical overhead lighting",
    "asc_fdl_link": null,
    "declared_by": "Morgan Chen, Director",
    "declared_at": "2026-05-15T10:08:00Z"
  }
}
```

`asc_fdl_link` is `null` — this is an AI-only production with no physical plates. No FDL connection exists.

---

## VES alignment note

The cinematic declaration fields align with the VES reference stack as documented in [`VES_VOCABULARY_ALIGNMENT.md`](VES_VOCABULARY_ALIGNMENT.md). Three fields — `parameter_origin`, `cinematic_intent`, and `prompt_excerpt` — have no VES equivalent because physical production never required them.

`parameter_origin` is the field that makes a cinematic declaration an authorship record rather than a production note. Without it, a framing declaration is a description. With it, it is evidence of a human decision.
