# The Camera Gap

**Version:** 1.0.0  
**For pipeline supervisors and standards committee members.**

This document establishes the architectural case for why authorship evidence for AI-generated content cannot travel inside the artifact. It is not a product pitch. It describes a structural problem that affects every credential system built on the assumption of a hardware capture event.

---

## 1. The Pipeline Failure Sequence

Consider a standard post-production delivery pipeline for a VFX facility:

1. **Generation** — An artist generates a plate in Runway or a ComfyUI workflow. The generation event occurs in a browser session or on localhost:8188.
2. **Color grade** — The plate is imported into DaVinci Resolve. A colorist applies the facility's ACES ODT and renders a new file. DaVinci Resolve does not preserve C2PA manifests. The embedded credential is gone.
3. **Compositing** — The colored plate is composited in Nuke. EXIF metadata is not written to EXR files. Any remaining embedded metadata from the generation step does not survive the Nuke write node.
4. **Transcode** — The composited shot is transcoded to ProRes for editorial. The transcode strips any remaining sidecar metadata that was not explicitly carried forward by the pipeline supervisor.
5. **CDN upload** — The delivered master is uploaded to the distributor's Content Hub or a delivery CDN. Standard CDN behavior: EXIF and XMP metadata are stripped at ingest to reduce payload size and prevent metadata leakage.

At the end of this sequence, the delivered file contains no embedded record of its origin. Every mechanism that attempted to carry the credential inside the artifact has failed at one of these five stages. This is not an edge case. This is a standard pipeline.

The tools are not at fault. DaVinci Resolve stripping C2PA manifests is not a bug — it is the expected behavior of a color science tool that renders new pixel data. Nuke not writing EXIF to EXR is not a bug — EXR is a professional format where the metadata schema is separate from the image data. CDN ingestion stripping metadata is not a bug — it is a deliberate security and performance decision.

The failure is architectural. The credential traveled inside the artifact. The artifact went through a pipeline. The pipeline destroyed the credential.

---

## 2. The C2PA Assumption

C2PA content credentials are designed for a world where a camera or a platform stamps the file at creation. That assumption is stated explicitly in the C2PA specification: the trust anchor is a hardware signing key in a physical capture device (camera, phone) or a platform signing key in a managed service (Adobe Firefly, Getty).

That assumption holds for hardware-captured media. A Sony Venice records a video frame. The camera's C2PA-compliant signing key stamps that frame at capture. The provenance of the frame is cryptographically bound to the hardware event that created it.

The assumption breaks for AI-generated content. There is no hardware capture event. The "creation" is a browser session — a sequence of HTTP requests between a user's browser and a generation API. There is no camera. There is no hardware signing key. There is no physical moment that can serve as the cryptographic trust anchor.

C2PA platform implementations — where a service like Adobe Firefly embeds a platform signature in the generated file — address this partially. The platform can sign the file at generation. But the platform signature documents what the platform produced. It does not document what the human decided. The two are different questions.

And the platform signature fails at stage 2 of the pipeline sequence above, for the same reason as every other in-file credential.

---

## 3. The Architectural Answer

If the credential cannot travel inside the file, the record must live outside the file — established before the first edit, anchored to a system that the pipeline cannot touch.

**What the TEP does:**

The authorship evidence record is created at the moment of generation and stored in a system separate from the artifact. The record is timestamped using OpenTimestamps — a Bitcoin blockchain anchor that creates a cryptographic timestamp predating any downstream modification. A perpetual hash (pHash) of the generated asset is computed at capture time and stored in the record. Neither the timestamp nor the pHash travels inside the asset.

When the asset completes its pipeline journey — color grade, compositing, transcode, CDN delivery — the external record still exists unchanged. The OpenTimestamps anchor still proves the record existed at the generation timestamp. The pHash can be compared against the delivered asset to confirm it is a recognizable derivative of the captured original (pHash survives moderate post-processing) or to flag that a significantly different asset was substituted (pHash fails for regenerated content).

The asset arrives at the distributor with no embedded metadata. The TEP arrives separately. The distributor's compliance reviewer opens the TEP and verifies: the record was created before the delivered file could have existed. The asset matches (within pHash tolerance) what was captured. A named human declared the creative decisions. The external record is the credential. The pipeline cannot destroy it because it never traveled through the pipeline.

**The regeneration attack.** The OpenTimestamps anchor directly addresses the primary attack against this architecture: an adversary who regenerates a near-identical output after the fact and argues there was no contemporaneous capture. The Bitcoin anchor exists before the argument is made. The anchor predates the regeneration. The argument fails.

---

## 4. What This Means for Conformant TEPs

A TEP is not an alternative to C2PA. The two answer different questions.

| Question | Answer |
|---|---|
| "Did this file come from a trusted source?" | C2PA content credentials |
| "What did the human decide when making this?" | TEP authorship evidence |

The TEP documents the human decision layer. C2PA authenticates the file. Both can coexist. A Level 2 conformant TEP will include a C2PA sidecar manifest as an additional record. The sidecar will document what the platform signed. The TEP will document what the human decided. Neither replaces the other.

The camera gap is the reason the two layers are necessary. For hardware-captured media, the camera is the trust anchor and C2PA is sufficient for the file-level question. For AI-generated media, there is no camera. The human attestation is the only available trust anchor — and it must be stored outside the asset, because the asset cannot be trusted to carry it through the pipeline.

This is not a weakness of the TEP architecture. It is its primary architectural argument. The record that cannot be stripped cannot be destroyed.

---

## Why pipeline supervisors should care

If your facility is compositing AI-generated plates, you are already building the pipeline that strips every in-file credential from AI-generated content. Your colorist is running Resolve. Your compositor is running Nuke. Your delivery pipeline is hitting a CDN.

The content your facility received was generated by someone. They may have excellent creative direction documentation. That documentation is gone by the time it reaches your facility — unless it was stored in a TEP that arrived alongside the asset.

The `fdl_link` in a Level 1 TEP is the bridge between the external AI authorship record and your facility's ASC FDL record. When an AI-generated plate arrives with a TEP, your pipeline can cross-reference the AI authorship declaration against your on-set FDL data without requiring LucidGrid access. The TEP is a file format. The verification tool is open source. The record is external. The pipeline cannot touch it.
