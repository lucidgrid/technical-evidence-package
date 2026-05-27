# Authorship Evidence Constitution

**Version:** 1.2  
**Effective Date:** May 2026  
**Status:** Active — Normative Reference  
**Maintained by:** LucidGrid Technologies — [lucidgrid.tech](https://lucidgrid.tech)  
**Contact:** hello@lucidgrid.tech  
**Changelog:** See [CHANGELOG.md](CHANGELOG.md)

---

## Preamble

Human authorship is a bedrock requirement of copyright. In AI-assisted creative production, that requirement does not disappear. It becomes harder to satisfy and harder to prove.

The US Copyright Office, the EU Court of Justice, and the UK Intellectual Property Office have each arrived at the same conclusion from different directions: copyright protection attaches to the human creative decisions embedded in a work, not to the computational process that produced it. A work generated without human authorship is not eligible for copyright protection under any major jurisdiction. A work produced with human direction — where a human selected the tool, shaped the prompt, set the parameters, evaluated the output, and exercised genuine editorial judgment — may qualify. The evidence of that direction is what makes the difference.

The production industry is generating AI-assisted content at volume. The documentation of the human creative decisions inside that production — who directed what, when, with which tools, across which assets — is not keeping pace. The gap between what studios produce and what they can prove is widening. This document names the architecture that closes it.

This constitution defines four things:

1. What counts as authorship evidence for AI-assisted creative works.
2. The mechanisms by which that evidence is captured.
3. The patterns that resemble evidence but do not qualify.
4. The format in which evidence is packaged for legal and compliance review.

This constitution is built as an extension of the production standards the VFX industry already operates against — OpenTrackIO, the ASC Framing Decision List, MovieLabs Ontology v2.8, Netflix VFX Shot/Version Naming, and the VES Transfer Spec. It does not replace those standards. It adds the authorship layer those standards never required — because a physical camera does not generate content autonomously, and until now, nothing in a production pipeline did either.

---

## Part I: Definitions

The following terms carry precise meanings throughout this document. Ambiguity in vocabulary produces ambiguity in evidence.

**Authorship Evidence.** A recorded artifact — a timestamped event, a captured parameter, a signed declaration — that corroborates a specific claim about human creative decision-making within a defined production workflow. Authorship evidence is not the creative act itself. It is the trace of the act, recorded in a form that an independent reviewer can examine without relying on the memory or goodwill of the person who made the claim.

**Human-in-the-Loop (HITL) Event.** A discrete, identifiable moment in a production workflow where a named human made a specific creative decision: selecting a prompt, choosing a seed value, configuring parameters, curating outputs from a generation set, drawing an inpainting mask, building a workflow graph, compositing an AI-generated element into a final deliverable. HITL events are the atomic units of authorship evidence.

**Technical Evidence Package (TEP).** The structured archive — a ZIP-compressed directory with a defined internal format — that contains all captured authorship evidence for a production project, organized by asset, by tool, by HITL event type, and by evidence type. It is the primary output of a conformant system and the primary input to any legal, compliance, or insurance review of an AI-assisted production. LucidGrid is the reference implementation. The format is open.

**Evidence Type.** The classification assigned to each captured HITL event based on the quality of the corroboration it provides. Three types exist: `machine_corroborated`, `contextually_inferred`, and `human_attested`. Each type carries a different evidentiary weight and a different set of legal implications. Evidence types are defined in full in Part IV.

**Paradigm.** The license and training-data posture of an AI tool used in production. Three paradigms exist: `proprietary`, `open_weights`, and `open_source`. Paradigm classification determines the liability posture the studio assumes when using a tool and governs the indemnification disclosure section of the TEP. Paradigm classification is defined in full in Part VI.

**Attestation.** A signed declaration by a named human party — a creative director, a VFX supervisor, an artist — that they exercised specific creative judgment during a specific production session. An attestation is a formal statement of accountability, not a self-assessment of quality. Its legal weight is not diminished by the absence of machine-corroborating evidence, provided the attestor is named, the scope is specific, and the declaration is tied to the project record.

**Provenance.** The documented chain of human creative decisions that produced an AI-assisted work. Provenance is not a property of the file. It is a property of the process that produced the file, recorded externally and separately from the file itself. A file can carry embedded metadata. That metadata is not provenance. Provenance is the external record of the decisions.

**Production Context.** A structured set of project-level identifiers that link a TEP to a studio's existing production management infrastructure. Production context fields follow Netflix VFX Shot/Version Naming Recommendations and MovieLabs Ontology v2.8 URN conventions. The production context block appears in MANIFEST.json at TEP export time and enables cross-reference between the TEP and any VES-compliant production management system.

**Cinematic Declaration.** A per-asset record of the framing and cinematography decisions made by the human directing an AI-generated video or image asset. Fields map to ASC FDL framing intent, OpenTrackIO motion vectors, and physical production vocabulary. The cinematic declaration is the evidence record for shot-level human creative direction in AI-generated content. Full field documentation: [`spec/CINEMATIC_DECLARATIONS.md`](spec/CINEMATIC_DECLARATIONS.md).

**Hybrid Production.** A production in which AI-generated visual elements are composited with physical plates captured under an on-set ASC Framing Decision List record. The `fdl_link` field in the cinematic declaration connects the AI-generated asset's framing decision to the physical production's stated framing intent — evidence of continuity across the physical/synthetic boundary.

**Adversary Model.** The set of bad-faith actors and technical conditions against which the evidence architecture must remain reliable. Three adversaries:

- **Metadata stripping** — the removal of embedded file metadata by distribution platforms or post-processing pipelines.
- **Regeneration attacks** — the recreation of a near-identical output after the original evidence was captured, used to argue that the original output required no human direction.
- **Post-hoc fabrication** — the construction of creative direction claims after the fact, without contemporaneous records.

Every architectural commitment in this constitution is a direct response to one or more of these adversaries.

---

## Part II: The Five Conditions for Valid Authorship Evidence

A recorded artifact constitutes valid authorship evidence under this constitution if and only if it satisfies all five of the following conditions.

**Condition 1 — Externality.**  
The evidence must be stored separately from the artifact it documents. Evidence embedded in a file — EXIF metadata, C2PA manifests, XMP sidecars inside the primary asset container — does not satisfy this condition alone. Embedded metadata is removed by every major social platform, by most distribution pipelines, and by any adversary with file-system access. Evidence stored in an external system — a separate archive, a cloud-hosted evidence record, a cryptographically anchored timestamp on a public blockchain — survives the loss of the primary artifact's embedded metadata. The TEP satisfies this condition. Embedded metadata alone does not.

**Condition 2 — Temporality.**  
The evidence must be timestamped at or near the time of the creative act it documents. A creative direction claim made in a document produced after delivery does not satisfy this condition. A captured browser event during an active generation session satisfies it. A signed attestation produced at delivery time satisfies it for the specific scope of the declaration, provided the attestor is named and the project record it references was established during production.

**Condition 3 — Specificity.**  
The evidence must be linkable to a specific asset or a specific creative decision within a defined project scope. Generic claims — "our team used AI tools responsibly" — do not satisfy this condition. A captured prompt associated with a specific asset UUID in a specific project satisfies it. A signed attestation covering all assets in a named project, with the specific HITL tags the attestor is claiming, satisfies it.

**Condition 4 — Attributability.**  
The evidence must be attributable to a named human party. An anonymous capture record satisfies Conditions 1, 2, and 3 but fails here. The human whose creative decision is being documented must be identifiable by name and by role.

**Condition 5 — Classification.**  
The evidence must carry an explicit label indicating its evidence type — `machine_corroborated`, `contextually_inferred`, or `human_attested`. Unlabeled evidence is inadmissible under this constitution because the reviewing party cannot evaluate its weight. The three evidence types are not a hierarchy of quality. They are a taxonomy of provenance.

---

## Part III: Evidence Capture Mechanisms

The following mechanisms are the pathways by which authorship evidence enters a conformant system. Each mechanism produces evidence that satisfies some or all of the five conditions.

**Mechanism 1 — Automated Workflow Capture (ComfyUI).**  
For ComfyUI workflows, the system captures the full workflow JSON, all node parameters, checkpoint identifiers, seed values, and generation outputs at the moment of production. This mechanism produces `machine_corroborated` evidence for all HITL tag types that correspond to captured events. It is the highest-fidelity mechanism in the system.

**Mechanism 2 — Browser Session Metadata Capture.**  
For AI tools accessed via the browser, a capture agent records browser-layer metadata at the time of the generation event: tool identity, detected prompt text, URL signature, session timestamp, and page-layer parameters where accessible. This mechanism produces `machine_corroborated` evidence for supported fields and `human_attested` evidence for fields that require user declaration.

**Mechanism 3 — User-Initiated Screenshot Capture.**  
The user captures a workspace state at a moment of their choosing. The capture records the timestamp, the active tool context, and a perceptual hash (pHash) of the captured image for integrity binding.

**Mechanism 4 — HITL Tag Declaration.**  
The user selects HITL tags to declare the creative actions taken in a session. Twelve tags are defined in the taxonomy: `prompt_engineering`, `seed_selection`, `parameter_tuning`, `regional_prompt`, `inpainting_mask`, `workflow_configuration`, `model_selection`, `prompt_iteration`, `output_curation`, `post_generation_edit`, `compositing_integration`, `project_supervision`. Tag selection triggers the evidence type assignment logic.

**Mechanism 5 — Attestation Statement.**  
The project-level attestation is a formal, signed declaration by a named human party covering a defined scope of creative direction across a set of project assets. It is stored in the TEP as `attestation.json`. It is an independent, project-scoped declaration.

**Mechanism 6 — Paradigm Classification.**  
At capture time, the system queries the Tool Registry to assign a paradigm classification to each tool used in the session. The classification is timestamped. If the tool's registry entry changes between capture and delivery, the TEP surfaces the delta explicitly.

**Mechanism 7 — Indemnification Disclosure.**  
The TEP records the indemnification posture of each tool at two points in time: the capture date and the export date. If the two states differ, the delta is reported with the change date and a plain-language description.

**Mechanism 8 — Perceptual Hash Binding.**  
A perceptual hash (pHash) of each captured asset — a hash that survives minor compression while detecting significant content changes — is computed at capture time and stored in the TEP's cryptographic proof JSON. pHash binding creates a tamper-evident link between the evidence record and the specific asset version it documents.

**Mechanism 9 — Cryptographic Timestamping.**  
The TEP's cryptographic proof JSON is anchored to the Bitcoin blockchain via OpenTimestamps at the time of package generation. The blockchain anchor provides a third-party, attack-resistant timestamp that proves the TEP existed in its documented form at or before a specific block height. This mechanism directly addresses the regeneration attack adversary.

**Mechanism 10 — IPTC XMP Sidecar Generation.**  
For each asset, the TEP generates an IPTC XMP sidecar file embedding the IPTC Digital Source Type field, the tool attribution, and the project reference. XMP sidecars provide a file-adjacent evidence record that complements the external TEP storage.

**Mechanism 11 — Production Context Linkage.**  
For projects where the project owner provides production management identifiers, the TEP records a `production_context` block in MANIFEST.json at export time. The block carries Netflix VFX Shot/Version naming fields, MovieLabs Ontology v2.8 URN identifiers, and a URL reference to the corresponding shot record in the studio's production management system.

---

## Part IV: The Three Evidence Types

Every HITL tag in every captured asset carries one of three evidence type labels. The labels are mutually exclusive per tag, assigned at capture time, and immutable in the TEP.

**Observed Truth (`machine_corroborated`).**  
A captured event directly corroborates the claimed creative action. A prompt was detected in browser metadata — the `prompt_engineering` tag is Observed Truth. A non-default seed value was captured in the ComfyUI workflow JSON — the `seed_selection` tag is Observed Truth. The system observed the event at the time it occurred.

*Evidentiary weight:* medium-high to high, depending on the HITL tag type. The captured event proves the action was taken at that time by the authenticated user. The inference from action to creative judgment is a legal question for the reviewer.

**Contextual Truth (`contextually_inferred`).**  
No direct capture event exists for the specific tag, but session-level context makes the claim highly plausible. Multiple distinct prompts were captured in the same session — the `prompt_iteration` tag is Contextual Truth. Multiple assets were captured in a single session — the `output_curation` tag is Contextual Truth. The inference is heuristic and labeled as such.

*Evidentiary weight:* medium. Contextual Truth is stronger than an unsigned verbal claim and weaker than a directly captured event.

**Declared Truth (`human_attested`).**  
No machine signal exists for the claimed creative action. The user declared the action via the HITL tag declaration mechanism. The declaration is a signed statement by an authenticated, named party.

*Evidentiary weight:* medium, with floor protections. Declared Truth is the correct evidence type for creative actions that occur outside the captured tool environment — compositing in a dedicated application, creative supervision of a vendor's output, editorial selection from a physical review session. The signed declaration is the appropriate and legally recognized method for documenting them.

The important asymmetry: Declared Truth is appropriate for actions that are inherently undocumentable by automated means. It is not a substitute for Observed Truth where automated capture is possible. A user who disables capture and relies entirely on Declared Truth produces a TEP with reduced evidentiary weight, and that reduction is visible to the reviewer.

---

## Part V: Eight Explicit Exclusions

The following patterns resemble authorship evidence but do not qualify under this constitution. Naming them explicitly is not a limitation. It is what makes the inclusions rigorous.

**Exclusion 1 — A vendor's IP indemnification is not authorship evidence.**  
Adobe Firefly's IP indemnification means Adobe assumes legal responsibility for copyright claims arising from Firefly's training data. It does not mean a human authored the output. The TEP discloses indemnification posture as legal context. It does not count indemnification as authorship evidence.

**Exclusion 2 — A tool's commercial use license is not authorship evidence.**  
Permission to use an AI tool for commercial purposes removes a vendor-side legal obstacle. It does not establish human creative direction.

**Exclusion 3 — A generated output is not authorship evidence.**  
The image, the video, the audio file — the artifact produced by the AI tool — is not evidence of human authorship. Its existence proves that a generation occurred. It does not prove who directed it, how they directed it, or what decisions they made.

**Exclusion 4 — Embedded file metadata is not sufficient authorship evidence.**  
EXIF data, embedded XMP, C2PA manifests inside a file's container are useful and should be preserved when available. They are not sufficient as standalone authorship evidence because every major distribution pipeline strips embedded metadata.

**Exclusion 5 — An AI tool's own attribution claims are not authorship evidence.**  
Some platforms produce outputs with embedded model attribution. This identifies the generating system. It does not document the human who directed the generation.

**Exclusion 6 — A prompt captured without HITL tagging is incomplete evidence.**  
A captured prompt event proves that a human typed a prompt at a specific time. It is not, by itself, evidence of the full scope of creative judgment exercised. The HITL tag declaration is what converts the prompt event from a timestamp into a contextualized authorship record.

**Exclusion 7 — A copyright registration is not authorship evidence for AI-assisted works.**  
A copyright registration acknowledges receipt of a claim. It does not constitute a determination that the registered work contains sufficient human authorship for protection. The TEP is the evidentiary support for the registration claim, not a document that registration replaces.

**Exclusion 8 — An undocumented verbal claim of creative direction is not authorship evidence.**  
"I directed that campaign" cannot be examined by an independent reviewer. Without a contemporaneous record — a captured session, a signed attestation tied to the project record — it does not satisfy Conditions 1, 2, or 5 of this constitution.

---

## Part VI: Paradigm Classification

Every AI tool used in a production workflow occupies one of three paradigm classifications, assigned by the Tool Registry and timestamped at assignment.

**`proprietary`.** The tool is a closed model operated by a vendor that controls the training data, the weights, and the inference pipeline. In some cases — Adobe Firefly, enterprise OpenAI — the vendor offers IP indemnification.

**`open_weights`.** The model weights are publicly available but the training data provenance is not fully disclosed or independently verifiable. FLUX.1, Stable Diffusion, LLaMA-family models. A studio running an open-weights model owns every line of training data liability. No vendor indemnification exists.

**`open_source`.** The model weights, training data inventory, and training process are fully disclosed and independently verifiable. Genuinely open-source generative models remain rare.

**Paradigm confidence tiers** (ComfyUI checkpoints):

| Tier | Method | Confidence |
|---|---|---|
| Tier 1 | SHA-256 hash matches registry entry | `verified` |
| Tier 2 | Filename matches registry pattern | `filename_match` |
| Tier 3 | Checkpoint unrecognized | `low` — defaults to `open_weights` |

The Tier 3 default is intentional. An unrecognized checkpoint is more likely to be a fine-tuned open-weights model than a proprietary or open-source model. Defaulting to `open_weights` at `low` confidence maximizes disclosed liability in the TEP.

---

## Part VII: Conservative Defaults Under Ambiguity

When evidence is ambiguous, this constitution defaults to the more conservative label.

- An unverified ComfyUI checkpoint defaults to `open_weights` at `low` confidence. Never promoted to `proprietary` without a Tier 1 exact match.
- An auto-detectable HITL tag that finds no corroborating event defaults to Declared Truth, not to an error.
- A contextual heuristic that fails its threshold defaults to Declared Truth, not to inferred.
- A tool not present in the registry defaults to `low` paradigm confidence and `open_weights` paradigm.
- A TEP with assets that have no HITL tags, no session captures, and no attestation surfaces those assets explicitly in the Executive Summary as zero-evidence assets. They are not silently omitted.

---

## Part VIII: The Technical Evidence Package Format

The TEP is a ZIP-compressed archive with a defined directory structure. Full format specification: [`spec/TEP_FORMAT.md`](spec/TEP_FORMAT.md).

**VES Transfer Spec Conformance.** TEP folder structure and file naming follow VES Transfer Specifications requirements. Legal characters: `[a-z][A-Z][0-9][.][_][-]` only. No spaces. No special characters. This ensures TEP packages can be delivered via Content Hub and any VES-compliant delivery infrastructure without renaming or modification.

**Top-level structure:**
```
/MANIFEST.json
/executive_summary.pdf
/cryptographic_proof.json
/attestation.json
/indemnification_disclosure.json
/assets/{asset_uuid}/
    /metadata.json
    /screenshot.png
    /xmp_sidecar.xmp
/workflow.json
```

**Capability levels:**

| Level | Contents |
|---|---|
| Level 0 | Structured ZIP + human-readable PDFs + MANIFEST.json |
| Level 1 | + `cryptographic_proof.json` with pHash and OpenTimestamps anchor |
| Level 2 (roadmap) | + C2PA sidecar manifests per asset |
| Level 3 (roadmap) | + cross-project lineage references |

Current production packages: Level 1. Current schema version: v1.9.0.

---

## Part IX: What the Package Does Not Claim

Every TEP includes a "Scope of This Package" section in the Executive Summary stating what the package documents and what it does not determine.

**The TEP does not determine copyright ownership.** Copyright ownership requires analysis by qualified counsel.

**The TEP does not determine copyright eligibility.** Whether the documented human authorship satisfies the threshold is a legal determination.

**The TEP does not verify the quality of the creative decisions documented.** Whether a prompt reflected meaningful creative judgment is a legal question.

**The TEP does not provide real-time verification of current vendor terms.** The indemnification disclosure documents what was true at capture and at export. Studios are responsible for verifying current vendor terms prior to delivery.

**The TEP does not constitute legal advice.** Recipients should consult qualified legal counsel before making legal, business, or regulatory decisions based on its contents.

---

## Part X: Conformance

**Conformance requires:**
- Storage of evidence external to the artifact (Condition 1)
- Timestamps on all evidence records at or near time of the creative act (Condition 2)
- Linkage of each evidence record to a specific asset by a stable identifier (Condition 3)
- Attribution of each evidence record to a named human party (Condition 4)
- Explicit evidence type labeling using `machine_corroborated`, `contextually_inferred`, or `human_attested` (Condition 5)
- A MANIFEST.json with SHA-256 hashes of all package files
- A plain-language executive summary accessible to non-technical reviewers
- Explicit disclosure of all AI tools used, with paradigm classification
- A "What This Package Does Not Claim" section

**Conformance does not require:**
- Use of any specific capture tool or platform
- OpenTimestamps anchoring (recommended but not required)
- IPTC XMP sidecar generation (recommended for redundancy)
- Any specific capture mechanism, provided the evidence satisfies the five conditions

The Tool Registry is an open resource. Studios and tool vendors may contribute detector entries via pull request without commercial engagement.

---

## Appendix A: Regulatory Context

The following frameworks shaped the requirements in this constitution. They are listed for reference, not as legal authority. Studios operating under these frameworks should consult qualified counsel.

| Framework | Relevance to TEP |
|---|---|
| US Copyright Office AI Guidance (2023–2026) | Registration requires disclosure of AI-generated content and demonstration of human authorship |
| *Thaler v. Perlmutter* (D.D.C. 2023, aff'd Fed. Cir. 2025) | Confirms autonomous AI creation is ineligible for copyright; human-directed workflows are legally distinct |
| EU AI Act Arts. 13–14 (Reg. 2024/1689) | Transparency and human oversight documentation for high-risk AI systems |
| EU Parliament PE 782.674 | AI-generated outputs without human authorship do not qualify for copyright under EU law |
| UK CDPA s.9(3) and HL Paper 267 | Human contribution documentation is the operative standard for AI-assisted works |
| Netflix Bifurcated AI Approval Framework | Distinguishes pre-approved AI tools from tools requiring case-by-case approval |
| MPA TPN v5.3.1 | Content protection requirements intersecting with provenance documentation |
| VES On-Set VFX Data Collection and Usage Guide (April 30, 2026) | Defines the production vocabulary that this constitution's schema extends |

---

## Appendix B: Architecture Alignment

This constitution implements the same five-pillar architecture that three other provenance systems independently arrived at:

| Pillar | LucidGrid TEP | Cisco Model Provenance Kit | Forensic DNA Paternity Testing | Double-Entry Accounting |
|---|---|---|---|---|
| Constitution before tool | CONSTITUTION.md precedes capture system | MPK constitution precedes detection software | Chain-of-custody protocol precedes sample | Double-entry rules precede ledger design |
| Multi-signal calibrated evidence | 3 evidence types × 12 HITL tags × OpenTimestamps | 5 weight signals vs. 111-pair benchmark | Per-locus paternity indices → composite score | Debit/credit reconciliation → balance |
| Tiered evidence pipeline | ComfyUI auto → browser session → signed declaration | Registry lookup → hash match → manual review | PCR amplification → electrophoresis → expert review | Journal entry → ledger → trial balance |
| External chain of custody | External storage + Bitcoin anchor + XMP sidecar | Fingerprints on Hugging Face separate from model | Sample chain of custody separate from analysis lab | Ledger separate from the account it records |
| Conservative defaults | Tier 3 → `open_weights`, missing corroboration → Declared Truth | Unknown model → maximum scrutiny flag | Match probability floored at forensic threshold | Unreconciled entries flagged, not silently carried |

---

## Part XI: VES Production Vocabulary Alignment

Full alignment documentation: [`spec/VES_VOCABULARY_ALIGNMENT.md`](spec/VES_VOCABULARY_ALIGNMENT.md).

**Summary table:**

| LucidGrid field | VES reference stack equivalent | Notes |
|---|---|---|
| `shot_type` | ASC FDL `framing_intent` canvas type | Shot-level framing declaration |
| `camera_movement` | OpenTrackIO `transforms[].rotation` motion vector | LucidGrid declares; OpenTrackIO measures |
| `focal_length_range` | OpenTrackIO `lens.pinholeFocalLength` | Artist's declared intent, not physical measurement |
| `depth_of_field` | OpenTrackIO `lens.fStop` + `lens.focusDistance` | Lens parameter, not FDL framing decision |
| `fdl_link` | `camera.fdlLink` URN (OpenTrackIO) | Bridge between AI framing declaration and physical FDL record |
| `omc_*` URNs | MovieLabs Ontology v2.8 | Cross-pipeline asset identification |
| `parameter_origin` | None | Unique to LucidGrid — no physical production equivalent exists |
| `cinematic_intent` | None | Unique to LucidGrid — free-text creative direction record |
| `prompt_excerpt` | None | Unique to LucidGrid — direct evidence of the human decision |

The three fields with no VES equivalent (`parameter_origin`, `cinematic_intent`, `prompt_excerpt`) have no equivalent because physical production never needed them. A camera does not generate content autonomously. `parameter_origin` is the field that converts a framing declaration from a production note into an authorship record.

---

*LucidGrid Technologies — [lucidgrid.tech](https://lucidgrid.tech)*  
*Schema: v1.9.0 | TEP format: Level 1*  
*This constitution will be updated as the legal environment develops and the TEP format evolves.*
