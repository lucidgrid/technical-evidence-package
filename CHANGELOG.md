# Changelog

All specification and schema changes are recorded here. Consumers of the open format should
check this file to determine whether a TEP they received was generated against an older schema
version and whether backward compatibility was preserved.

Format: version, date, summary, affected files, backward compatibility.

---

## [1.1.0] — 2026-05-20

### Added
- `spec/CAMERA_GAP.md` — The architectural argument for external provenance storage.
  Establishes the in-file credential failure as the primary case for why authorship evidence
  cannot travel inside the artifact.
- `spec/VES_VOCABULARY_ALIGNMENT.md` — Field-by-field mapping between VES guide vocabulary
  and TEP fields. Five alignment points covering OpenTrackIO, ASC FDL, MovieLabs OMC v2.8,
  Netflix VFX naming, and VES Transfer Spec nomenclature.
- `schema/manifest.schema.json` — Formal JSON Schema for MANIFEST.json, schema version v1.9.0.
- `schema/metadata.schema.json` — Formal JSON Schema for per-asset metadata.json.
- `registry/tools/wan.json`, `cogvideox.json`, `hunyuanvideo.json` — Open-weights video
  generation model registry entries.

### Changed
- CONSTITUTION.md rewritten for public GitHub audience. No changes to normative content
  (five conditions, eight exclusions, three evidence types, paradigm classification).
- TOOL_REGISTRY.md updated with `detection_mechanism` column and `capture_type` field.

**Backward compatible:** Yes. All v1.0.0 TEPs remain valid against the v1.1.0 schema.

---

## [1.0.0] — 2026-05-20

### Added
- Initial public release.
- `CONSTITUTION.md` — The Authorship Evidence Constitution, normative reference.
- `spec/TEP_FORMAT.md` — Technical Evidence Package format specification.
- `spec/HITL_EVIDENCE_TAXONOMY.md` — Human-in-the-Loop evidence taxonomy.
- `spec/EVIDENCE_TYPES.md` — Condensed evidence type reference.
- `spec/EXCLUSION_TAXONOMY.md` — Explicit exclusions from the evidence definition.
- `spec/CINEMATIC_DECLARATIONS.md` — Cinematic parameter declaration schema.
- `registry/TOOL_REGISTRY.md` — The open tool registry, 18 detectors at v1.0.0.
- `registry/tools/` — 19 JSON tool definition files.
- `examples/ai_only_project/` — Complete worked TEP example.
- `docs/quickstart/FOR_PIPELINE_SUPERVISORS.md`
- `docs/quickstart/FOR_LEGAL_PROFESSIONALS.md`

**Backward compatible:** N/A — initial release.
