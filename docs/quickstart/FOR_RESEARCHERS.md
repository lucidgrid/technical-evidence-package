# For Researchers

This guide is for researchers working in AI provenance, copyright, digital forensics, or creative AI governance who want to understand the TEP format or use it as a reference model.

---

## What this repository defines

The Technical Evidence Package specifies a format for packaging and verifying evidence of human creative decisions in AI-assisted production workflows. It addresses a concrete legal problem: courts and regulators require evidence that a human exercised sufficient creative control over an AI-assisted work for copyright to attach. The TEP format defines what that evidence looks like, how it is structured, and how it is anchored to an external chain of custody.

The format is grounded in five architectural commitments documented in [`CONSTITUTION.md`](../../CONSTITUTION.md):

1. Constitution precedes tool — the specification governs the implementation, not the reverse.
2. Multi-signal calibrated evidence — no single signal is sufficient.
3. Tiered capture pipeline — automated, assisted, and manual declaration mechanisms.
4. External chain of custody — OpenTimestamps Bitcoin anchor, IPTC XMP sidecar, signed attestation.
5. Conservative defaults with named exclusions.

---

## Key documents for researchers

| Document | What it covers |
|---|---|
| [`CONSTITUTION.md`](../../CONSTITUTION.md) | The evidentiary theory and eight named exclusions |
| [`spec/TEP_FORMAT.md`](../../spec/TEP_FORMAT.md) | Package structure, field definitions, conformance requirements |
| [`spec/HITL_EVIDENCE_TAXONOMY.md`](../../spec/HITL_EVIDENCE_TAXONOMY.md) | 12 HITL tags, three evidence tiers, machine corroboration logic |
| [`spec/EVIDENCE_TYPES.md`](../../spec/EVIDENCE_TYPES.md) | Observed Truth, Contextual Truth, Declared Truth — definitions and weight |
| [`spec/EXCLUSION_TAXONOMY.md`](../../spec/EXCLUSION_TAXONOMY.md) | Eight patterns that resemble evidence but do not qualify |
| [`spec/CAMERA_GAP.md`](../../spec/CAMERA_GAP.md) | The five-stage failure model for embedded credentials; the regeneration attack |
| [`spec/VES_VOCABULARY_ALIGNMENT.md`](../../spec/VES_VOCABULARY_ALIGNMENT.md) | Vocabulary alignment to VES, MovieLabs OMC, and Netflix VFX naming |
| [`schema/manifest.schema.json`](../../schema/manifest.schema.json) | Formal JSON Schema v1.9.0 for MANIFEST.json |
| [`schema/metadata.schema.json`](../../schema/metadata.schema.json) | Formal JSON Schema v1.9.0 for per-asset metadata.json |
| [`examples/ai_only_project/`](../../examples/ai_only_project/) | Worked example TEP with real field values |

---

## The evidentiary theory in brief

Copyright protection in AI-assisted works depends on demonstrating that a human being made creative decisions that shaped the output in ways distinguishable from pure computational generation. The US Copyright Office and EU AI Act have converged on the same requirement: the evidence of those decisions must exist, must be contemporaneous, must be attributable, and must be legible to a non-technical reviewer.

The TEP format operationalizes this requirement into a structured evidence package. The twelve HITL tags are not a checklist — they are a vocabulary for the types of creative intervention that courts and compliance reviewers have accepted as material. The three evidence tiers (Observed, Contextual, Declared Truth) reflect the epistemic quality of each piece of evidence and its expected weight under adversarial review.

The full theoretical basis is in [`CONSTITUTION.md`](../../CONSTITUTION.md), Part I–III.

---

## Using the schemas

Both JSON schemas are formally specified in JSON Schema Draft 2020-12. The `$id` URIs resolve to their canonical locations on GitHub:

- [`schema/manifest.schema.json`](../../schema/manifest.schema.json)
- [`schema/metadata.schema.json`](../../schema/metadata.schema.json)

Any conformant TEP can be validated against these schemas using any compliant JSON Schema validator.

---

## Contributing to the specification

The format is open under CC BY 4.0. Specification changes go through pull request review — see [`CONTRIBUTING.md`](../../CONTRIBUTING.md) for scope and process. Research findings that challenge or extend the evidentiary theory in CONSTITUTION.md are especially welcome.

---

*For questions: [lucidgrid.tech](https://lucidgrid.tech)*
