# Quickstart: Legal Professionals

**Audience:** In-house counsel, clearance coordinators, E&O underwriters, rights registrars evaluating AI-generated content for commercial clearance.  
**Time to read:** 12 minutes.

---

## What a TEP is

A Technical Evidence Package (TEP) is a structured, machine-verifiable archive that documents the provenance of AI-generated creative assets. It is produced by the studio's production pipeline and delivered as a single ZIP file.

A TEP is not a legal opinion. It is a structured evidence file — the digital equivalent of a production report, chain-of-title abstract, or copyright registration exhibit. It answers three questions that the law requires studios to answer when AI-generated content is included in a commercial production:

1. **What did the AI generate?** Which tool, which model, at what time, producing what output.
2. **What did a human decide?** The documented creative choices made by a named, authenticated individual — choices that may establish authorship, creative control, or transformation.
3. **Who is accountable?** A signed project-level declaration by a named individual with a defined creative role.

---

## The evidence hierarchy

Every creative action documented in a TEP carries one of three evidence labels, assigned at capture time:

### Observed Truth (`machine_corroborated`)

The system directly captured the event — the prompt text, the seed value, the model file — at the moment it occurred. The captured value is stored in `metadata.json`, bound to the asset by perceptual hash (pHash), and timestamped via OpenTimestamps (Bitcoin blockchain anchor).

**Evidentiary weight:** Medium-high to high. Proves the action was taken at that time by the authenticated session user. The inference from action to creative judgment is a legal question for the reviewer — the system does not make that determination.

### Contextual Truth (`contextually_inferred`)

No direct capture event exists for the specific action, but session-level context makes the claim highly plausible. Example: multiple distinct prompts captured in one session → `prompt_iteration` inferred as Contextual Truth. The heuristic basis is stored in `metadata.json`.

**Evidentiary weight:** Medium. Stronger than an unsigned verbal claim. Weaker than a directly captured event.

### Declared Truth (`human_attested`)

No machine signal exists. The user declared the action via the HITL tag interface. The declaration is stored in `metadata.json` as a human-attested tag with no `captured_value`. The attestor is identified by name and role in `attestation.json`.

**Evidentiary weight:** Medium, with floor protections. Declared Truth is not a deficiency. It is the correct evidence type for creative actions that occur outside the captured tool environment — compositing in a dedicated application, creative supervision of a vendor's output, editorial selection from a physical review session.

**The asymmetry:** Declared Truth is appropriate for actions that are inherently undocumentable by automated means. It is not a substitute for Observed Truth where automated capture was possible. A TEP that relies entirely on Declared Truth where Observed Truth was available has reduced evidentiary weight — and that reduction is visible to the reviewer through the `evidence_completeness_score` field.

---

## Reading the evidence completeness score

Every TEP includes an `evidence_completeness_score` in `MANIFEST.json`:

```json
"evidence_completeness_score": {
  "status": "green",
  "score": 0.84,
  "denominator": 12
}
```

| Status | Score | Interpretation |
|--------|-------|----------------|
| `green` | ≥ 0.80 | Strong evidence coverage. Most actions have Observed or Contextual Truth. |
| `amber` | 0.50–0.79 | Adequate. Some assets rely on Declared Truth. Acceptable for most commercial contexts. |
| `red` | < 0.50 | Weak. High proportion of Declared Truth or zero-evidence assets. May not satisfy Copyright Office or specialty distributor standards. |

The score is a heuristic. It informs your risk assessment — it does not determine copyrightability or clear an asset for release.

---

## Indemnification status

Each tool used in the production has an indemnification status in `indemnification_disclosure.json`. This is a dual-status record: it captures the vendor's stated IP position both at the time of capture and at the time of export. A `status_delta: true` flag indicates the vendor changed their terms between those two dates.

| Status | Meaning |
|--------|---------|
| `vendor_indemnified` | Vendor explicitly indemnifies commercial use of outputs for eligible plan holders. Verify plan eligibility. |
| `enterprise_indemnified` | Indemnification available on enterprise tier or with enterprise agreement only. Standard plan users receive no coverage. |
| `no_indemnity` | Vendor provides no IP indemnification. Studio bears full liability for all outputs. |
| `unknown` | Status could not be confirmed from public terms as of the last verification date. Verify before commercial use. |

**What indemnification covers and does not cover:** Vendor IP indemnification (where it exists) typically covers third-party claims arising from the vendor's training data. It does not cover: infringement claims arising from the studio's prompt inputs; personality rights, privacy, or defamation claims; claims arising from output modification; claims outside the vendor's stated indemnification scope. Read the specific vendor terms in each case.

---

## Cryptographic integrity

Every TEP includes a `cryptographic_proof.json` file containing:

- **SHA-256 hash** of each asset file
- **pHash (perceptual hash)** — a 64-bit DCT-based hash that survives minor compression and format conversion while detecting significant content changes
- **OpenTimestamps proof** — a Bitcoin blockchain anchor proving the package existed at the stated timestamp

The OpenTimestamps proof can be independently verified against the Bitcoin blockchain using the OpenTimestamps CLI (`ots verify`). This proof is not controlled by LucidGrid and does not depend on LucidGrid's continued operation to remain valid.

The `MANIFEST.json` `contents` array lists SHA-256 hashes of every file in the package. Any tampering with any file will produce a hash mismatch detectable on re-verification.

---

## The attestation

`attestation.json` contains the project-level signed declaration. Key fields:

- **`attestor.name`** — the named individual signing the attestation
- **`attestor.role`** — their creative role (`creative_director`, `vfx_supervisor`, `artist`, `producer`, `other`)
- **`attestation_statement`** — free-text minimum 50 characters authored by the attestor
- **`signature_method`** — `session_authenticated` (authenticated via platform identity verification) or `manual_signature` (handwritten signature process)
- **`scope.hitl_tags_claimed`** — the specific creative actions the attestor is signing off on

The `ip_address_hash` field stores a SHA-256 hash of the attestor's IP address at the time of attestation. It does not store the IP address itself. It is provided to support session authentication verification, not as a definitive identification mechanism.

---

## Model paradigm and IP risk

The `paradigm_classification` field in each `metadata.json` record indicates the type of AI model used:

| Paradigm | IP risk profile |
|----------|----------------|
| `proprietary` | Vendor controls the model. IP claims arising from training data are directed to the vendor. Indemnification (where available) covers these claims. |
| `open_weights` | Model weights are publicly released. The studio runs the model directly. No vendor to indemnify training data claims. Studio bears full liability. |
| `open_source` | Model weights and code are publicly released under an open license. Same risk profile as open_weights for IP purposes. |

Open-weights models require more conservative E&O positioning. A studio using ComfyUI with a third-party checkpoint has no vendor indemnification available for training data claims against that checkpoint's outputs.

---

## What a TEP does not do

- **Does not determine copyrightability.** Whether AI-generated content meets the human authorship threshold is a legal question. The TEP documents the evidence that informs that determination — it does not make it.
- **Does not guarantee clearance.** A TEP with a Green evidence score does not mean the content is cleared for any specific use. Clearance depends on the legal analysis of the evidence.
- **Does not constitute legal advice.** This document and the TEP format are informational tools for structuring and preserving evidence.
- **Does not replace chain of title.** A TEP documents provenance of AI-generated elements. It does not replace standard chain-of-title documentation for the production as a whole.

---

## Where to go next

- `spec/TEP_FORMAT.md` — full technical specification of the TEP format
- `spec/HITL_EVIDENCE_TAXONOMY.md` — the twelve creative action types and their evidence classification logic
- `spec/EVIDENCE_TYPES.md` — detailed evidence type definitions and evidentiary weight analysis
- `spec/EXCLUSION_TAXONOMY.md` — exclusion categories (assets explicitly excluded from TEP coverage)
- `examples/ai_only_project/` — a complete annotated example TEP
- `registry/TOOL_REGISTRY.md` — all supported tools and their indemnification status
