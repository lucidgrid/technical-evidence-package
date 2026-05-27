# TEP Format Specification

**Schema version:** v1.9.0  
**Capability level:** 1  
**Spec version:** 1.0.0

A conformant Technical Evidence Package (TEP) is a ZIP-compressed archive with a defined internal structure. This document specifies what a conformant TEP must contain, how it must be structured, and what each component means.

---

## Required Sidecar Files

Every conformant TEP must contain the following six files at the package root:

| File | Description | Required |
|---|---|---|
| `MANIFEST.json` | Package index and integrity contract | Yes |
| `executive_summary.pdf` | Human-readable compliance summary | Yes |
| `cryptographic_proof.json` | Asset hashes and OpenTimestamps anchor | Level 1+ |
| `attestation.json` | Project-level signed declaration | Yes |
| `indemnification_disclosure.json` | Dual-status vendor terms record | Yes |
| `workflow.json` | ComfyUI workflow graph (where applicable) | Conditional |

`workflow.json` is required when the production used ComfyUI. It is omitted for browser-captured assets only.

---

## Directory Structure

```
{package_id}.zip
├── MANIFEST.json
├── executive_summary.pdf
├── cryptographic_proof.json
├── attestation.json
├── indemnification_disclosure.json
├── workflow.json                        (conditional)
└── assets/
    └── {asset_uuid}/
        ├── metadata.json
        ├── screenshot.png               (if captured)
        └── xmp_sidecar.xmp
```

### Naming conventions

All file and folder names must use only legal characters: `[a-zA-Z0-9._-]`. No spaces. No special characters. No duplicate file or folder names within any directory level.

This ensures TEP packages can be delivered via Content Hub and any VES-compliant delivery infrastructure (VES Transfer Spec) without renaming or modification.

`{asset_uuid}` must be a version-4 UUID formatted as a lowercase hyphenated string (e.g., `550e8400-e29b-41d4-a716-446655440000`).

---

## MANIFEST.json

The MANIFEST.json is the integrity contract. Every file in the package is listed with its SHA-256 hash. A consumer verifying the package checks each file against the manifest before reading any other content.

**Required fields:**

```json
{
  "schema_version": "1.9.0",
  "package_id": "uuid-v4",
  "created_at": "ISO-8601 UTC timestamp",
  "creator": {
    "name": "string",
    "role": "string",
    "system": "string"
  },
  "project": {
    "id": "uuid-v4",
    "name": "string",
    "created_at": "ISO-8601 UTC timestamp"
  },
  "assets": [
    {
      "asset_id": "uuid-v4",
      "asset_type": "image | video | audio | 3d",
      "files": [
        {
          "path": "assets/{asset_uuid}/metadata.json",
          "sha256": "hex string"
        }
      ]
    }
  ],
  "evidence_completeness_score": {
    "status": "green | amber | red",
    "score": 0.0,
    "denominator": 0
  },
  "hitl_summary": {
    "observed_truth_count": 0,
    "contextual_truth_count": 0,
    "declared_truth_count": 0,
    "zero_evidence_asset_count": 0
  },
  "contents": [
    {
      "path": "MANIFEST.json",
      "sha256": "hex string"
    }
  ]
}
```

**Optional fields:**

```json
{
  "production_context": {
    "show_id": "string",
    "episode": "string",
    "sequence": "string",
    "scene": "string",
    "shot_id": "string",
    "version_name": "string",
    "task": "string",
    "vendor_id": "string",
    "version_number": "string",
    "omc_creative_work_id": "urn:movielabs:creative-work:...",
    "omc_asset_id": "urn:movielabs:asset:...",
    "omc_version_id": "urn:movielabs:version:...",
    "production_management_system": "shotgrid | flow | ftrack | custom",
    "external_shot_ref": "https://..."
  },
  "ves_taxonomy_enabled": false,
  "fdl_link": "urn:asc:fdl:...",
  "production_ref": "string",
  "ves_alignment_version": "1.9.0"
}
```

The `production_context` block is omitted entirely when no production context is provided. It is never present as an empty object.

`ves_taxonomy_enabled: true` activates VES-conditional fields across the package (production context, FDL references, VFX shot naming). Studios that do not follow VES naming conventions omit this field entirely.

---

## cryptographic_proof.json

Required at Level 1 and above. Contains:

```json
{
  "schema_version": "1.9.0",
  "package_id": "uuid-v4",
  "generated_at": "ISO-8601 UTC timestamp",
  "asset_hashes": [
    {
      "asset_id": "uuid-v4",
      "phash": "hex string",
      "sha256": "hex string",
      "captured_at": "ISO-8601 UTC timestamp"
    }
  ],
  "tep_sha256": "hex string",
  "ots_proof": "base64-encoded OpenTimestamps .ots file content"
}
```

`phash` is a 64-bit perceptual hash computed using the DCT method (pHash algorithm). It survives minor compression and format conversion while detecting significant content changes.

`ots_proof` contains the full OpenTimestamps proof anchored to the Bitcoin blockchain. Verification: `ots verify` using the OpenTimestamps CLI against the `tep_sha256` file.

---

## attestation.json

```json
{
  "schema_version": "1.9.0",
  "project_id": "uuid-v4",
  "attestation_id": "uuid-v4",
  "attestor": {
    "name": "string",
    "role": "creative_director | vfx_supervisor | artist | producer | other",
    "ip_address_hash": "sha256 hex string"
  },
  "attestation_timestamp": "ISO-8601 UTC timestamp",
  "scope": {
    "asset_ids": ["uuid-v4"],
    "hitl_tags_claimed": ["prompt_engineering", "output_curation"]
  },
  "attestation_statement": "string (minimum 50 characters)",
  "signature_method": "session_authenticated | manual_signature"
}
```

The `attestation_statement` is free-text authored by the attestor. The schema enforces a minimum length of 50 characters to prevent empty attestations.

---

## indemnification_disclosure.json

```json
{
  "schema_version": "1.9.0",
  "generated_at": "ISO-8601 UTC timestamp",
  "tools": [
    {
      "tool_name": "string",
      "tool_slug": "string",
      "paradigm": "proprietary | open_weights | open_source",
      "capture_indemnification_status": "vendor_indemnified | enterprise_indemnified | no_indemnity | unknown",
      "capture_date": "ISO-8601 date",
      "export_indemnification_status": "vendor_indemnified | enterprise_indemnified | no_indemnity | unknown",
      "export_date": "ISO-8601 date",
      "status_delta": false,
      "delta_summary": null,
      "status_changed_on": null,
      "indemnification_source_url": "https://...",
      "notes": "string"
    }
  ]
}
```

When `status_delta: true`, `delta_summary` and `status_changed_on` are required.

---

## metadata.json (per asset)

The primary HITL evidence record for a single asset.

```json
{
  "schema_version": "1.9.0",
  "asset_id": "uuid-v4",
  "asset_type": "image | video | audio | 3d",
  "ai_tool": "string",
  "tool_slug": "string",
  "tool_version": "string | null",
  "paradigm_classification": "proprietary | open_weights | open_source",
  "paradigm_confidence": "verified | filename_match | low",
  "lifecycle_stage_number": 1,
  "modification_level": "generated | edited | composited",
  "capture_method": "api_capture | file_extraction | manual_upload",
  "input_description": "string",
  "output_description": "string",
  "timestamp_captured": "ISO-8601 UTC timestamp",
  "phash": "hex string",
  "human_actions": [
    {
      "tag": "prompt_engineering",
      "evidence_type": "machine_corroborated | contextually_inferred | human_attested",
      "captured_value": "string | null"
    }
  ],
  "hitl_tags": ["prompt_engineering"],
  "user_notes": "string | null",
  "browser_metadata": {
    "url": "string",
    "user_agent": "string | null"
  },
  "cinematic_declaration": null
}
```

**`cinematic_declaration`** — present for video and image assets when the user has declared cinematic parameters:

```json
{
  "shot_type": "wide | medium | close | extreme_close | insert",
  "camera_movement": "static | dolly | pan | tilt | crane | handheld | zoom",
  "focal_length_range": "wide | normal | telephoto | extreme_telephoto",
  "depth_of_field": "shallow | medium | deep",
  "color_space": "string | null",
  "dynamic_range": "string | null",
  "lighting_intent": "string | null",
  "parameter_origin": "human_specified | human_directed_ai_executed | ai_generated | mixed",
  "cinematic_intent": "string",
  "prompt_excerpt": "string",
  "fdl_link": "urn:asc:fdl:... | null",
  "declared_by": "string",
  "declared_at": "ISO-8601 UTC timestamp"
}
```

`fdl_link` is `null` for AI-only productions. Populated only for hybrid productions where the AI-generated asset's framing connects to a physical production's ASC FDL record.

---

## Capability Levels

| Level | Description | Status |
|---|---|---|
| Level 0 | Structured ZIP + human-readable PDFs + MANIFEST.json | Minimum viable |
| Level 1 | + `cryptographic_proof.json` with pHash and OpenTimestamps anchor | Current production |
| Level 2 | + C2PA sidecar manifests per asset | Roadmap — Q4 2026 |
| Level 3 | + Cross-project lineage references | Roadmap |

All currently produced packages are Level 1. The Level 2 C2PA integration will add a `c2pa/` directory at the package root with per-asset content credentials. Level 1 and Level 0 packages remain valid against all future schemas.

---

## Versioning

The `schema_version` field in MANIFEST.json is the authoritative version indicator. The format uses semantic versioning: `MAJOR.MINOR.PATCH`.

- **MAJOR** increment: breaking changes (removed required fields, changed field types, changed evidence type taxonomy).
- **MINOR** increment: new optional fields, new optional sidecar files.
- **PATCH** increment: clarifications, documentation changes, no schema changes.

All v1.x packages are backward compatible with each other. A consumer built against v1.9.0 can read any v1.x package.
