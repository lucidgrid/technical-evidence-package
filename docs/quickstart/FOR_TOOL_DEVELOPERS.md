# For Tool Developers

This guide is for developers building systems that produce, consume, or validate Technical Evidence Packages (TEPs) — including capture tools, pipeline integrations, validators, and export systems.

---

## What conformance means

A conformant TEP producer is a system that outputs ZIP archives that pass validation against:

- [`schema/manifest.schema.json`](../../schema/manifest.schema.json) — for `MANIFEST.json`
- [`schema/metadata.schema.json`](../../schema/metadata.schema.json) — for each `assets/{asset_id}/metadata.json`

Conformance does not require a commercial relationship with LucidGrid. The format is open under CC BY 4.0.

---

## Required package structure

```
/MANIFEST.json
/cryptographic_proof.json
/attestation.json
/indemnification_disclosure.json
/assets/{asset_uuid}/
    /metadata.json
    /screenshot.png          (if captured)
    /xmp_sidecar.xmp         (if generated)
```

File and directory naming must follow VES Transfer Spec legal character set: `[a-z][A-Z][0-9][.][_][-]`. No spaces. No special characters. Asset directories must be named by UUID.

Full structure specification: [`spec/TEP_FORMAT.md`](../../spec/TEP_FORMAT.md).

---

## Key implementation requirements

### MANIFEST.json

- `schema_version` must be a semver string matching the schema version used for validation.
- `contents` must include a SHA-256 hash entry for every file in the package, including MANIFEST.json itself (self-referential).
- `evidence_completeness_score.score` is a float between 0 and 1 representing the proportion of HITL events that reached machine corroboration. Producers that do not compute ECS should set `status: "not_computed"` rather than omitting the field.

### Per-asset metadata.json

- `asset_id` must match the UUID used as the directory name under `assets/`.
- `human_actions` array must contain one entry per HITL tag claimed. Each entry must specify `evidence_type` as one of `machine_corroborated`, `contextually_inferred`, or `human_attested`.
- `captured_value` is required when `evidence_type` is `machine_corroborated`. It must contain the actual captured value (prompt text, seed integer, model filename, etc.), not a summary.
- Do not claim `machine_corroborated` for events that were not directly captured. Use `contextually_inferred` or `human_attested` instead.

### HITL tags

12 valid tags: `prompt_engineering`, `seed_selection`, `parameter_tuning`, `regional_prompt`, `inpainting_mask`, `workflow_configuration`, `model_selection`, `prompt_iteration`, `output_curation`, `post_generation_edit`, `compositing_integration`, `project_supervision`.

Only include tags for creative events that actually occurred. Do not include all twelve tags for every asset. Full tag definitions and evidence tier logic: [`spec/HITL_EVIDENCE_TAXONOMY.md`](../../spec/HITL_EVIDENCE_TAXONOMY.md).

---

## Detection mechanism integration

If your tool integrates with the detector registry, implement the `detection_mechanism` field as:

- `browser` — URL-pattern matching via browser extension
- `localhost_api` — local API interception (e.g., ComfyUI `/api/prompt`)
- `manual` — user-declared tool use

To contribute a detector entry for your tool: [`registry/CONTRIBUTING_DETECTORS.md`](../../registry/CONTRIBUTING_DETECTORS.md).

---

## Reference examples

A worked example TEP is available at [`examples/ai_only_project/`](../../examples/ai_only_project/). It demonstrates:

- A conformant `MANIFEST.json` with ECS and HITL summary
- A `metadata.json` with mixed evidence tiers (`machine_corroborated` and `contextually_inferred`)
- A signed `attestation.json`
- An `indemnification_disclosure.json` covering multiple tools

LucidGrid is the reference implementation. TEPs produced by LucidGrid conform to this specification at schema version v1.9.0.

---

## Validation

Validate packages against the published JSON Schemas using any compliant JSON Schema Draft 2020-12 validator. The `$id` URIs in both schemas resolve to their canonical locations on GitHub.

---

*For questions: [lucidgrid.tech](https://lucidgrid.tech)*
