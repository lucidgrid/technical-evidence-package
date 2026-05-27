# HITL Evidence Taxonomy

**Version:** 1.0.0  
**Schema version:** v1.9.0

The Human-in-the-Loop (HITL) evidence taxonomy defines the twelve creative action types that a TEP can document, the three evidence types that classify them, and the rules for assigning evidence types at capture time.

---

## The Three Evidence Types

Every HITL tag in every captured asset carries one of three evidence type labels. The labels are mutually exclusive per tag, assigned at capture time, and immutable in the TEP.

### Observed Truth (`machine_corroborated`)

**Definition.** A captured event directly corroborates the claimed creative action. The system observed the event at the time it occurred.

**Capture mechanism.** Browser session metadata, ComfyUI workflow JSON, API interception.

**Verification method.** The captured event is stored in `metadata.json` and referenced in `cryptographic_proof.json`. The pHash binds it to the specific asset version. The OpenTimestamps anchor proves it existed at the capture timestamp.

**Evidentiary weight.** Medium-high to high. Proves the action was taken at that time by the authenticated session user. The inference from action to creative judgment is a legal question for the reviewer, not a determination made by the system.

**Example values.**  
- `prompt_engineering`: prompt text captured from browser DOM — `"captured_value": "medium shot, telephoto lens, shallow focus, motivated push"`
- `seed_selection`: non-default seed captured from ComfyUI KSampler node — `"captured_value": "3847291056"`
- `model_selection`: checkpoint filename captured from ComfyUI checkpoint loader — `"captured_value": "flux1-dev-Q8_0.gguf"`

---

### Contextual Truth (`contextually_inferred`)

**Definition.** No direct capture event exists for the specific tag, but session-level context makes the claim highly plausible. The inference is heuristic. It is labeled as such.

**Capture mechanism.** Session-level pattern analysis: multiple prompt events, multiple assets, session duration, tool switching patterns.

**Verification method.** The heuristic basis is stored in `metadata.json`. The reviewer can evaluate the session context that produced the inference.

**Evidentiary weight.** Medium. Stronger than an unsigned verbal claim. Weaker than a directly captured event. Weight increases when multiple contextual signals corroborate the same claim.

**Example values.**  
- `prompt_iteration`: multiple distinct prompts captured in a single session
- `output_curation`: multiple assets captured in a single session, fewer exported than captured
- `parameter_tuning`: parameter values captured that deviate from tool defaults

---

### Declared Truth (`human_attested`)

**Definition.** No machine signal exists for the claimed creative action. The user declared the action via the HITL tag declaration mechanism. The declaration is a signed statement by an authenticated, named party.

**Capture mechanism.** User-initiated HITL tag selection in the capture interface.

**Verification method.** The declaration is stored in `metadata.json` as a human-attested tag with no `captured_value`. The attestor is identified by name and role in `attestation.json`.

**Evidentiary weight.** Medium, with floor protections. Declared Truth is not a deficiency. It is the correct evidence type for creative actions that occur outside the captured tool environment — compositing in a dedicated application, creative supervision of a vendor's output, editorial selection from a physical review session. A signed declaration by a named, accountable party is the appropriate and legally recognized method for documenting these actions.

**The asymmetry.** Declared Truth is appropriate for actions inherently undocumentable by automated means. It is not a substitute for Observed Truth where automated capture is possible. A TEP that relies entirely on Declared Truth where Observed Truth was available has reduced evidentiary weight, and that reduction is visible to the reviewer.

---

## The Twelve HITL Tags

| Tag | Consumer label | Auto-detectable | Evidence type (default) |
|---|---|---|---|
| `prompt_engineering` | Prompt authoring | Yes (browser DOM) | Observed Truth |
| `seed_selection` | Seed selection | Yes (ComfyUI JSON) | Observed Truth |
| `parameter_tuning` | Parameter configuration | Partial (tool-dependent) | Contextual Truth |
| `regional_prompt` | Regional prompt / masking | Partial | Contextual Truth |
| `inpainting_mask` | Inpainting / masking | Partial | Contextual Truth |
| `workflow_configuration` | Workflow design | Yes (ComfyUI JSON) | Observed Truth |
| `model_selection` | Model selection | Yes (ComfyUI checkpoint) | Observed Truth |
| `prompt_iteration` | Prompt iteration | Contextual (multiple prompts) | Contextual Truth |
| `output_curation` | Output curation | Contextual (multiple assets) | Contextual Truth |
| `post_generation_edit` | Post-generation editing | No | Declared Truth |
| `compositing_integration` | Compositing / integration | No | Declared Truth |
| `project_supervision` | Creative supervision | No | Declared Truth |

---

## Evidence Type Determination Logic

At capture time, the system assigns evidence types using the following priority chain:

```
For each HITL tag declared by the user:

  1. Attempt machine corroboration
     — Does a captured event directly correspond to this tag?
     — YES → evidence_type: "machine_corroborated"

  2. If no direct event: attempt contextual inference
     — Does session-level context make this tag highly plausible?
     — YES (above heuristic threshold) → evidence_type: "contextually_inferred"

  3. If heuristic fails or tag is inherently undocumentable:
     — evidence_type: "human_attested"
```

The conservative default: ambiguous cases drop to `human_attested`, not to an error or to Contextual Truth. The absence of a machine signal does not invalidate the human declaration. It reclassifies it.

---

## Boundary Conditions

**`prompt_engineering` vs. `prompt_iteration`.** If a single prompt is captured, the tag is `prompt_engineering` (Observed Truth). If multiple distinct prompts are captured in the same session, `prompt_iteration` is added as a separate Contextual Truth tag. Both can be present simultaneously.

**`output_curation` threshold.** Output curation is inferred contextually when the number of captured assets exceeds the number exported. The threshold is session-configurable. Default: any session where more assets were captured than exported.

**`compositing_integration` and `post_generation_edit`.** These tags document creative actions that occur in external applications — DaVinci Resolve, After Effects, Photoshop — outside the capture environment. They are inherently Declared Truth. The declaration is the evidence.

**`project_supervision`.** A creative director or VFX supervisor who reviewed and approved AI-generated outputs without directly operating a tool uses `project_supervision` to declare their oversight role. This tag is always Declared Truth and is typically accompanied by the project-level attestation statement.

---

## Known Limitations

The taxonomy does not currently cover:

- **Voice direction for audio/video** — the specific creative decisions made when directing ElevenLabs or Suno outputs (tone, pacing, character voice) are not tagged at the sub-tool level. Covered by `prompt_engineering` at the top level.
- **3D topology decisions** — specific artistic choices about polygon distribution, rigging setup, and UV unwrapping in Meshy or TripoAI outputs are not tagged. Covered by `parameter_tuning` generically.
- **Multi-model pipeline orchestration** — when a workflow chains multiple models (FLUX.1 image → Runway video → ElevenLabs audio), the hand-off decisions between stages are covered by `workflow_configuration` and `project_supervision` but not with per-stage granularity.

These limitations are documented in `CONSTITUTION.md` §14 Known Gaps and are open research questions.
