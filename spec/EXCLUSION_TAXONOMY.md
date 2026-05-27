# Exclusion Taxonomy

**Version:** 1.0.0

This document catalogs what the TEP deliberately does not document and why. Every exclusion is stated explicitly, with the reason. A system that counts everything counts nothing. The exclusions are what make the inclusions rigorous.

This document is the primary safeguard against misreading a TEP as a legal opinion. Attorneys advising clients on AI IP diligence should read this alongside [`../legal/EVIDENCE_WEIGHT_GUIDE.md`](../legal/EVIDENCE_WEIGHT_GUIDE.md).

---

## Exclusion 1 — Copyright ownership determination

**What is excluded.** The TEP does not determine who owns the copyright in an AI-assisted work.

**Why.** Copyright ownership is a legal conclusion that requires analysis by qualified counsel, accounting for jurisdiction, work-for-hire provisions, employment contracts, assignment agreements, and the specific human authorship evidence in the package. The TEP provides the evidence. It does not render the conclusion.

**What the TEP does instead.** Documents the human creative decisions that form the evidentiary basis for an ownership claim. Provides that documentation in a form a legal reviewer can examine.

---

## Exclusion 2 — Copyright eligibility determination

**What is excluded.** The TEP does not determine whether a work is eligible for copyright registration or protection.

**Why.** Copyright eligibility is a threshold question determined by the Copyright Office or the applicable jurisdiction's authority. Whether the documented human authorship satisfies that threshold — whether the creative decisions documented were "sufficiently original" or rose above "mere selection" — is a legal determination, not a documentation determination.

**What the TEP does instead.** Documents what was captured. The Evidence Completeness Score signals the coverage and quality of that documentation. The legal conclusion is for counsel and the relevant authority.

---

## Exclusion 3 — Fair use assessment

**What is excluded.** The TEP does not assess whether the use of third-party training data or third-party reference materials in the production constitutes fair use.

**Why.** Fair use analysis is a four-factor legal test applied by qualified counsel. The TEP cannot perform that analysis.

**What the TEP does instead.** Records the paradigm classification and indemnification status of each tool. For open-weights models, flags the training data transparency status. The studio and its counsel determine the fair use implications.

---

## Exclusion 4 — Chain-of-title conclusions

**What is excluded.** The TEP does not conclude whether a studio has clear title to the AI-assisted work it produced.

**Why.** Chain-of-title analysis requires examining contracts, assignments, work-for-hire agreements, and jurisdiction-specific rules that are outside the scope of any automated documentation system.

**What the TEP does instead.** Documents the named humans who made creative decisions, their roles, and the production context they operated within. That documentation is an input to chain-of-title analysis, not its conclusion.

---

## Exclusion 5 — Business justification for AI use

**What is excluded.** The TEP does not record why a studio chose to use AI tools, how that decision was made, or the business rationale for it.

**Why.** Business justification is a governance question, not an evidence question. The TEP's scope is the human creative decisions within a production workflow, not the organizational decisions upstream of it.

**What the TEP does instead.** Documents what was done. The production context block records the project-level identifiers. The tool registry records the indemnification posture. Neither constitutes a business justification record.

---

## Exclusion 6 — Model training data provenance

**What is excluded.** The TEP does not independently verify what training data was used to produce any AI model's capabilities.

**Why.** Training data provenance for commercial AI models is not publicly disclosed in sufficient detail for independent verification. The TEP records the `training_data_status` field from the Tool Registry — which states whether training data is `disclosed`, `partially_disclosed`, `undisclosed`, or `contested` — but does not audit or verify the underlying claims.

**What the TEP does instead.** Records the training data transparency status from the registry. When a model's training data is `undisclosed` or `contested`, the TEP flags this in the indemnification disclosure. The implication for liability is for the studio and its counsel to determine.

---

## Exclusion 7 — Indemnification coverage adequacy

**What is excluded.** The TEP does not assess whether a vendor's IP indemnification is adequate for the studio's specific legal situation.

**Why.** Indemnification coverage is a legal question that depends on the scope of the coverage, the studio's jurisdiction, the nature of the potential claim, and the terms of any relevant insurance policy. A vendor saying "we provide IP indemnification" does not mean the specific claim the studio faces is covered.

**What the TEP does instead.** Records the indemnification status at capture and export dates, the source URL for that status, and the delta if the status changed. The adequacy determination is for qualified counsel.

---

## Exclusion 8 — Regulatory compliance certification

**What is excluded.** The TEP does not certify that a production is compliant with any regulatory framework — the EU AI Act, US Copyright Office requirements, MPA TPN, or any other standard.

**Why.** Compliance certification requires interpretation of regulatory requirements against the specific facts of a production, performed by qualified counsel or a certified auditor. The TEP is a documentation instrument, not a certification authority.

**What the TEP does instead.** Provides structured documentation designed to support compliance review. The regulatory context in [`../legal/REGULATORY_CONTEXT.md`](../legal/REGULATORY_CONTEXT.md) describes what the TEP addresses and what it does not address for each framework. A compliance officer or attorney can read both and determine the coverage gap.

---

## The scope boundary

The TEP stops at evidence. The legal conclusions from that evidence belong to counsel.

This boundary is not a limitation. It is what makes the TEP defensible. A documentation system that claims to determine copyright ownership or certify compliance is a system that can be argued against on the merits of those determinations. A documentation system that records what happened, when, by whom, and what kind of evidence that constitutes — and explicitly refuses to draw the legal conclusions — is a system whose factual record is much harder to dispute.

The eight exclusions above are not things the TEP cannot do. They are things it will not do, because doing them would undermine the evidentiary value of what it does.
