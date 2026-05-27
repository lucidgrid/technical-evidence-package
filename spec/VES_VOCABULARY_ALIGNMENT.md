# VES Vocabulary Alignment

**Version:** 1.1.0 (schema v1.9.0)  
**Reference stack:** VES On-Set VFX Data Collection and Usage Guide (April 30, 2026)

This document maps the TEP field vocabulary to the VES guide reference stack. Five alignment points are documented. For each alignment point: the VES guide reference, the TEP field, the current implementation status, and the schema version that introduced it.

This is the document pipeline supervisors and standards committee members read first.

---

## The alignment strategy

The VES On-Set VFX Data Collection and Usage Guide defines the operational vocabulary for the VFX production pipeline: OpenTrackIO/CamDKit for camera tracking, the ASC Framing Decision List for framing intent, MovieLabs OMC v2.8 for cross-pipeline asset identification, Netflix VFX Shot/Version Naming for delivery conformance, and VES Transfer Spec nomenclature for file and folder naming.

These are not peripheral standards. They are the daily vocabulary of every pipeline supervisor at any major VFX facility.

LucidGrid's schema speaks this vocabulary structurally. The alignment documented here makes that explicit — so a pipeline engineer reading the TEP spec can see the correspondence without inference.

---

## Alignment Point 1 — OpenTrackIO / CamDKit (SMPTE RiS-OSVP)

**VES reference:** OpenTrackIO camera tracking data, CamDKit implementations  
**TEP fields:** `cinematic_declarations.*`  
**Status:** Implemented (v1.6.0)

| LucidGrid field | OpenTrackIO equivalent | Notes |
|---|---|---|
| `camera_movement` | `transforms[].rotation` motion vector | OpenTrackIO measures physical rotation. LucidGrid declares artistic intent. Both document the same underlying decision from different layers. |
| `focal_length_range` | `lens.pinholeFocalLength` | OpenTrackIO records the physical focal length in mm. LucidGrid records the artist's declared intent range (wide / normal / telephoto / extreme). |
| `depth_of_field` | `lens.fStop` + `lens.focusDistance` | The combination of fStop and focus distance determines DoF on a physical camera. LucidGrid records the artistic intent (shallow / medium / deep). |
| `fdl_link` | `camera.fdlLink` URN | The bridge between AI-generated framing declarations and physical production FDL records. Present in hybrid productions only. |

**The gap the VES standard does not cover:** OpenTrackIO measures physical lens and motion parameters from hardware sensors. For AI-generated content, there are no physical sensors and no hardware data. The TEP's cinematic declaration fields record the human's artistic direction — not what sensors measured, but what the artist intended. The two answers are the same creative intent expressed at different layers.

---

## Alignment Point 2 — ASC Framing Decision List (FDL)

**VES reference:** ASC FDL for framing intent documentation  
**TEP fields:** `shot_type`, `fdl_link`, `cinematic_intent`  
**Status:** Implemented (v1.6.0, extended v1.9.0)

| LucidGrid field | ASC FDL equivalent | Notes |
|---|---|---|
| `shot_type` | `framing_intent` canvas type | The FDL records the physical production's declared framing intent at the canvas level. The TEP records the same declaration for AI-generated content. |
| `fdl_link` | `id` / `label` canvas identifier | A URN that references the physical production's FDL canvas. Present only in hybrid productions where an AI-generated plate's framing must be traceable to the on-set FDL record. |
| `cinematic_intent` | None — no FDL equivalent | Free-text capture of the artist's stated creative direction. No FDL equivalent exists because the FDL describes physical production intent, not the artist's narrative direction. |

**The `fdl_link` field is the architectural bridge.** In hybrid productions — where AI-generated elements composite with physical plates captured under an on-set FDL record — the `fdl_link` in the AI asset's cinematic declaration connects the AI authorship evidence to the physical production's framing data. A pipeline supervisor receiving a hybrid TEP can cross-reference the AI plate's declared framing against the physical production's FDL canvas without requiring LucidGrid access.

---

## Alignment Point 3 — MovieLabs Ontology for Media Creation (OMC) v2.8

**VES reference:** MovieLabs OMC for cross-pipeline asset identification  
**TEP fields:** `omc_creative_work_id`, `omc_asset_id`, `omc_version_id` (in `production_context`)  
**Status:** Implemented (v1.4.0)

| LucidGrid field | OMC v2.8 equivalent | Notes |
|---|---|---|
| `omc_creative_work_id` | `Creative Work` entity URN | Identifies the creative work the production is contributing to. Format: `urn:movielabs:creative-work:{uuid-v4}` |
| `omc_asset_id` | `Asset` entity URN | Identifies the specific asset within the creative work. Format: `urn:movielabs:asset:{uuid-v4}` |
| `omc_version_id` | `Version` entity URN | Identifies the specific version of the asset. Format: `urn:movielabs:version:{uuid-v4}` |

OMC URNs are populated by the project owner from their production management system. LucidGrid records them as provided — it does not generate or validate external production IDs. Studios that do not use MovieLabs OMC URNs omit these fields. The fields are optional in the schema.

---

## Alignment Point 4 — Netflix VFX Shot/Version Naming

**VES reference:** Netflix VFX Shot/Version Naming Recommendations  
**TEP fields:** `show_id`, `episode`, `sequence`, `scene`, `shot_id`, `version_name`, `task`, `vendor_id`, `version_number` (in `production_context`)  
**Status:** Implemented (v1.4.0)

The `production_context` block in MANIFEST.json follows the Netflix naming convention directly:

| LucidGrid field | Netflix naming convention | Example |
|---|---|---|
| `show_id` | Show identifier | `AGM` |
| `episode` | Episode number | `104` |
| `sequence` | Sequence identifier | `TCC` |
| `scene` | Scene number | `065` |
| `shot_id` | Shot identifier | `0010` |
| `version_name` | Full version string | `AGM_104_065_010_comp_NFX_v001` |
| `task` | Task type | `comp` |
| `vendor_id` | Vendor identifier | `NFX` |
| `version_number` | Version number | `v001` |

Studios following the Netflix naming convention can populate these fields directly from their production management system. A compliance reviewer receiving the TEP can cross-reference the TEP against the studio's ShotGrid, Autodesk Flow, or ftrack record without requiring LucidGrid access.

---

## Alignment Point 5 — VES Transfer Spec Nomenclature

**VES reference:** VES Transfer Specifications file and folder naming requirements  
**TEP fields:** All TEP file and folder names  
**Status:** Implemented (v1.0.0)

The TEP ZIP structure uses only VES Transfer Spec legal characters: `[a-zA-Z0-9._-]`. No spaces. No special characters. No duplicate names within any directory level.

| VES requirement | TEP implementation |
|---|---|
| Legal characters only | All file/folder names use `[a-zA-Z0-9._-]` |
| No spaces | Enforced at TEP generation |
| No duplicate names | Enforced at TEP generation |
| Consistent case | Lowercase for directory names, UPPERCASE for package-level JSON files |

This ensures TEP packages can be delivered via Content Hub and any VES-compliant delivery infrastructure without renaming or modification. A facility that already follows VES Transfer Spec naming in its pipeline does not need to handle TEP packages differently.

---

## Fields with no VES equivalent

Three fields in the cinematic declaration schema have no equivalent in OpenTrackIO, the ASC FDL, or any other physical production standard:

| Field | Reason no VES equivalent exists |
|---|---|
| `parameter_origin` | Physical production never needed it. A camera does not generate content autonomously. No cinematographer has declared whether the camera or an AI system decided the focal length — because cameras do not decide focal lengths. `parameter_origin` records whether cinematic parameters were `human_specified`, `human_directed_ai_executed`, `ai_generated`, or `mixed`. It is the field that converts a framing declaration from a production note into an authorship record. |
| `cinematic_intent` | Free-text capture of the artist's narrative direction for the AI-generated asset. Physical production records physical measurements, not the artist's intent expressed in prose. |
| `prompt_excerpt` | The prompt text that operationalized the declared framing. Physical production does not involve prompts. This is direct evidence of the human creative decision expressed as text. |

These are not gaps in the VES standard. They are boundaries. The VES standard describes what the physical production did. The TEP describes what the human decided. In hybrid productions they describe the same shot from different evidence layers.

---

## What this means for the VES generative AI provenance chapter

When the VES Technology Committee drafts its generative AI provenance chapter, the vocabulary alignment documented here provides a ready mapping from existing VES field definitions to the authorship evidence layer.

The TEP does not replace the VES standard. It extends it to content that has no physical capture event. The five alignment points above are the five places where the extension connects back to the existing vocabulary.

A pipeline supervisor reading the TEP specification can locate every alignment point in the VES guide they already use. The authorship layer speaks the same language. The extension is additive.
