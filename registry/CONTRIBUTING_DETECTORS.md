# Contributing a Tool Detector

Any platform, studio, or individual can contribute a detector for an AI tool not yet covered by the community registry. Contributions do not require commercial engagement with LucidGrid.

LucidGrid maintains the reference implementation, which covers all categories listed below. Community contributions extend coverage to tools not in the reference implementation and to regional or specialized platforms the core team has not prioritized.

---

## What a detector entry covers

A registry entry defines:

- The tool's canonical URL patterns for browser-based detection
- The tool's paradigm classification (`proprietary`, `open_weights`, `open_source`)
- The tool's indemnification status at a verified date
- The HITL tags the tool's workflow supports
- The detection mechanism (`browser`, `localhost_api`, or `manual`)

---

## Category coverage

The table below shows the categories the registry is designed to cover, with canonical examples per category. LucidGrid's reference implementation provides full detection coverage across all rows marked **Full**. Community submissions for tools in any category are welcome.

| Category | Canonical Examples | Detection Mechanism | LucidGrid Coverage |
|---|---|---|---|
| Image generation — proprietary | Midjourney, Adobe Firefly, DALL-E 3 | `browser` | Full |
| Image generation — open weights | Stable Diffusion (A1111, Forge), FLUX | `localhost_api` / `browser` | Full |
| Video generation — proprietary | Runway, Sora, Kling AI, Luma, Pika | `browser` | Full |
| Video generation — open weights | WAN 2.1, CogVideoX, HunyuanVideo | `manual` | Full |
| Local inference orchestrator | ComfyUI + custom checkpoints | `localhost_api` | Full |
| Audio generation | ElevenLabs, Suno, Udio | `browser` | Full |
| 3D generation | Meshy, TripoAI, Kaedim | `browser` | Full |
| Multimodal / next-generation | GPT-4o Image, Gemini Image, Ideogram | `browser` | Partial — submissions open |

**Full** means browser detection, HITL tag mapping, paradigm classification, and indemnification status are all established in the reference implementation. Entries for tools in these categories go through standard review.

**Partial** means detection logic exists for some tools in the category but the category boundary is still being defined. Submissions here are especially valuable.

---

## The detector JSON schema

Two reference examples are available in `registry/tools/`:
- [`comfyui.json`](tools/comfyui.json) — `localhost_api` detection, open-weights paradigm
- [`runway.json`](tools/runway.json) — `browser` detection, proprietary paradigm

Every file submitted to `registry/tools/` must conform to this schema:

```json
{
  "tool_name": "string — canonical display name",
  "tool_slug": "string — lowercase, underscores, no spaces",
  "category": "image | video | audio | 3d | multimodal",
  "vendor": "string — legal entity name",
  "paradigm": "proprietary | open_weights | open_source",
  "indemnification_status": "vendor_indemnified | enterprise_indemnified | no_indemnity | unknown",
  "indemnification_last_verified": "YYYY-MM-DD",
  "indemnification_source_url": "https://...",
  "training_data_status": "disclosed | partially_disclosed | undisclosed | contested",
  "detection_mechanism": "browser | localhost_api | manual",
  "url_patterns": ["string"],
  "supported_hitl_tags": ["string"],
  "capture_type": "automated | assisted_manual | manual_only",
  "native_c2pa": "native | partial | unsupported",
  "notes": "string"
}
```

### Required fields

All fields are required except `notes`.

### `detection_mechanism` values

| Value | Meaning |
|---|---|
| `browser` | Detected via browser extension URL matching and DOM inspection |
| `localhost_api` | Detected via local API interception (e.g., ComfyUI `localhost:8188`) |
| `manual` | No automated detection; user manually declares tool use |

### `supported_hitl_tags`

List only the tags the tool's workflow actively supports. Do not list all twelve tags for every tool. An audio tool does not support `inpainting_mask`.

Valid values: `prompt_engineering`, `seed_selection`, `parameter_tuning`, `regional_prompt`, `inpainting_mask`, `workflow_configuration`, `model_selection`, `prompt_iteration`, `output_curation`, `post_generation_edit`, `compositing_integration`, `project_supervision`

Full tag definitions: [`spec/HITL_EVIDENCE_TAXONOMY.md`](../spec/HITL_EVIDENCE_TAXONOMY.md).

---

## Submission requirements

Open a pull request with:

1. **The JSON file** — `registry/tools/{tool_slug}.json` conforming to the schema above.

2. **A verification note** in the PR description confirming:
   - URL patterns were tested against a live session (list the date and what was tested)
   - Indemnification status was verified against the source URL listed (quote the relevant terms section)

3. **Indemnification source** — direct URL to the tool's terms of service section covering IP indemnification (or confirming its absence).

4. **If updating an existing entry** — note what changed and why (e.g., vendor changed terms, URL pattern updated, new HITL tags added).

---

## Review criteria

Entries are evaluated on:

- **Accuracy** — URL patterns must be specific enough to avoid false positives. `*.com` is not acceptable. `app.runway.ml` is acceptable.
- **Verification** — indemnification status must be sourced from current vendor terms, not news coverage or secondary sources.
- **Completeness** — all required fields must be present and non-null.
- **HITL tag accuracy** — tags listed must correspond to real workflow capabilities, not aspirational features.

Entries with `indemnification_status: "unknown"` are accepted if the submitter has made a reasonable effort to locate the tool's terms and the status is genuinely undetermined.

**Target review time: five business days.**

---

## Indemnification status definitions

| Status | Meaning |
|---|---|
| `vendor_indemnified` | Vendor explicitly assumes IP liability for commercial use of generated outputs (e.g., Adobe Firefly) |
| `enterprise_indemnified` | Indemnification available only on enterprise tier, not standard plans (e.g., OpenAI Enterprise) |
| `no_indemnity` | Vendor terms explicitly disclaim IP responsibility; studio bears full liability |
| `unknown` | Terms do not address indemnification; submitter was unable to locate a clear statement |

---

## Reference implementation

LucidGrid is the reference implementation of the TEP format and this detector registry schema. The LucidGrid capture system includes:

- Browser extension URL detection across all categories listed above
- Localhost API detection for ComfyUI and local inference pipelines
- Paradigm classification for all covered tools
- Indemnification status tracking with quarterly verification cycles
- HITL tag mapping validated against live tool workflows

Community contributions supplement this coverage. The format and schema defined here are what the LucidGrid system implements internally. Contributions that conform to the schema are production-compatible with LucidGrid-generated TEPs from day one.

If a tool you need is covered by LucidGrid's reference implementation but not yet available via community submission, contact the team: [lucidgrid.tech](https://lucidgrid.tech).
