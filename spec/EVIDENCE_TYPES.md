# Evidence Types — Quick Reference

**For the full treatment:** [`HITL_EVIDENCE_TAXONOMY.md`](HITL_EVIDENCE_TAXONOMY.md)  
**For legal weight analysis:** [`../legal/EVIDENCE_WEIGHT_GUIDE.md`](../legal/EVIDENCE_WEIGHT_GUIDE.md)

---

Three evidence types exist in the TEP format. Every HITL tag in every captured asset carries one. They are a taxonomy of provenance, not a hierarchy of quality.

---

## Observed Truth (`machine_corroborated`)

**What it means.** The system captured a direct event that corroborates the claimed creative action.

**Capture scenario.** A prompt was detected in browser metadata at the time of generation. A non-default seed value was captured in the ComfyUI workflow JSON. A specific model checkpoint was identified in the graph loader node.

**What it proves.** That this specific action occurred at this specific time by the authenticated session user.

**What it does not prove.** That the action reflected meaningful creative judgment. That question is for the reviewer.

---

## Contextual Truth (`contextually_inferred`)

**What it means.** No direct capture event exists, but session-level context makes the claim highly plausible. The inference is heuristic. The label communicates that difference to the reviewer.

**Capture scenario.** Multiple distinct prompts were captured in the same session — the output curation claim is plausible. Parameter values deviate from tool defaults — the parameter tuning claim is plausible.

**What it proves.** That the session context is consistent with the claimed action. Not that the action was directly observed.

**What it does not prove.** The specific content of the creative decision. That is visible only where the captured event value is available.

---

## Declared Truth (`human_attested`)

**What it means.** No machine signal exists for the claimed creative action. A named, authenticated human declared the action.

**Capture scenario.** A VFX supervisor composited AI-generated elements into a scene in DaVinci Resolve — outside any capturable tool environment. An art director reviewed and approved AI outputs from a studio review session. A creative director supervised an AI vendor's generated assets for a campaign.

**What it proves.** That a named, accountable person has formally declared they took a specific creative action within a defined production scope.

**What it does not prove.** The machine-level details of that action. Those are not capturable by definition.

---

## Comparing the three types

| | Observed Truth | Contextual Truth | Declared Truth |
|---|---|---|---|
| Machine corroboration | Direct event captured | Session context only | None |
| Requires human declaration | No (automatic) | No (automatic) | Yes |
| Evidentiary weight | Medium-high to high | Medium | Medium with floor |
| Appropriate for | In-tool creative actions | Inferred session patterns | Out-of-tool creative actions |
| Example tag | `prompt_engineering` | `prompt_iteration` | `compositing_integration` |

---

## The evidence completeness score

A TEP's Evidence Completeness Score (ECS) summarizes the distribution of evidence types across all captured assets:

- **Green** — high proportion of Observed Truth; attestation coverage complete; zero zero-evidence assets.
- **Amber** — mix of Observed and Contextual Truth; attestation present; some assets with limited evidence.
- **Red** — significant Declared Truth reliance; missing attestation coverage; zero-evidence assets present.

The ECS is a triage signal, not a legal determination. A TEP with Amber status can still support a strong authorship claim. A TEP with Red status is a signal to investigate the specific assets with low evidence coverage before delivery.
