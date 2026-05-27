# Quickstart: Pipeline Supervisors

**Audience:** VFX supervisors, pipeline TDs, production supervisors who need to integrate AI provenance capture into a production pipeline.  
**Time to read:** 10 minutes.

---

## What a TEP is and why it exists

A Technical Evidence Package (TEP) is a structured ZIP archive that documents, in machine-verifiable form, what AI tools generated what assets, what creative decisions a human made, and who signed off.

Studios are increasingly required to prove that AI-generated content meets distributor clearance standards, copyright registration thresholds, and emerging regulatory disclosure requirements (EU AI Act, AAAA contract provisions). A TEP replaces manual evidence gathering — typically 3–5 business days of spreadsheets, screenshots, and emails — with a single exportable package.

A TEP is not a legal opinion. It is evidence that a lawyer, clearance coordinator, or rights registry can evaluate. The stronger your evidence, the stronger your legal position.

---

## The three things a TEP must prove

1. **What was generated** — which AI tool, which model, at what time, producing what output.
2. **What a human decided** — the creative choices a named, authenticated person made, documented with the highest achievable evidence type.
3. **Who signed off** — a project-level attestation by a named individual with a defined creative role.

These three things map directly to the three core files: `metadata.json` (per asset), `attestation.json` (project level), and `MANIFEST.json` (integrity contract).

---

## The evidence hierarchy

Every human creative action in a TEP carries one of three evidence type labels:

| Label | What it means | Evidentiary weight |
|-------|--------------|-------------------|
| **Observed Truth** (`machine_corroborated`) | The system directly captured the event at the moment it occurred. | Medium-high to high |
| **Contextual Truth** (`contextually_inferred`) | No direct capture, but session-level context makes the claim highly plausible. | Medium |
| **Declared Truth** (`human_attested`) | No machine signal. The user declared the action via the HITL tag interface. | Medium, with floor protections |

Your goal as a pipeline supervisor: maximize Observed Truth wherever automated capture is possible. Use Declared Truth only for creative actions that genuinely cannot be captured by machine (compositing in DaVinci Resolve, creative supervision of a vendor's output, editorial selection from a physical review session).

A TEP that relies entirely on Declared Truth where Observed Truth was available has reduced evidentiary weight — and that reduction is visible to the reviewer.

---

## Integration points

### ComfyUI

LucidGrid's ComfyUI integration captures workflows automatically via `/api/prompt` interception and the LiteGraph graph reader. No changes to existing ComfyUI workflows required. The captured data includes:

- Full workflow JSON (node graph)
- Checkpoint filename → paradigm classification
- KSampler seed values
- Prompt text from CLIPTextEncode nodes

Set `LUCIDGRID_BACKEND_URL` in the extension settings and ComfyUI assets are captured on generation.

### Browser-based tools (Midjourney, Runway, Pika, Kling, etc.)

The LucidGrid browser extension captures prompt text, parameter values, and screenshots from the browser DOM. Capture is semi-automated: the extension detects the tool, extracts what it can, and prompts the user to confirm and add HITL tags for actions that cannot be inferred.

### Manual upload

For assets generated outside a captured environment (a vendor's facility, a tool without a detector), use manual upload. The `capture_method` field is set to `manual_upload` and the evidence type for all tags defaults to `human_attested`.

---

## Evidence completeness score

Every exported TEP includes an `evidence_completeness_score` in `MANIFEST.json`:

- **Green** (≥ 0.80): Strong evidence coverage. Most actions have Observed or Contextual Truth.
- **Amber** (0.50–0.79): Adequate. Some assets rely on Declared Truth. Acceptable for most distributor contexts.
- **Red** (< 0.50): Weak. High proportion of Declared Truth or zero-evidence assets. May not satisfy Copyright Office or Netflix standards.

Target Green for primary productions. Amber is acceptable for reference assets and secondary deliverables.

---

## The production context block (VES studios only)

If your studio follows VES naming conventions, add a `production_context` block to `MANIFEST.json`. This links the TEP to your production management system and enables cross-reference against the ASC FDL (Framing Decision List) record for the shot.

If you do not follow VES naming: omit `production_context` and omit `ves_taxonomy_enabled`. Do not include either as empty fields.

---

## Schema validation

All TEP files are validated against JSON Schema v1.9.0 at export time. If a package fails validation, LucidGrid will not export it. To manually validate a package against the published schemas:

```bash
npm install -g ajv-cli

# Validate MANIFEST
ajv validate -s schema/manifest.schema.json -d MANIFEST.json

# Validate a per-asset metadata file
ajv validate -s schema/metadata.schema.json -d assets/{asset_uuid}/metadata.json
```

---

## Common integration mistakes

| Mistake | Correct approach |
|---------|----------------|
| Adding `production_context: {}` for non-VES productions | Omit the field entirely |
| Setting `fdl_link: null` for AI-only productions | Omit the field entirely |
| Using `capture_method: "local_capture"` | Valid values: `api_capture`, `file_extraction`, `manual_upload` |
| Treating Declared Truth as a deficiency | It is the correct type for actions outside the capture environment |
| Exporting before running schema validation | Always validate before delivery |

---

## Where to go next

- `spec/TEP_FORMAT.md` — full format specification
- `spec/HITL_EVIDENCE_TAXONOMY.md` — the twelve HITL tags and their evidence types
- `examples/ai_only_project/` — a complete annotated example
- `registry/TOOL_REGISTRY.md` — all supported tools, their paradigm classifications, and indemnification status
- `docs/quickstart/FOR_LEGAL_PROFESSIONALS.md` — the legal audience version of this document
