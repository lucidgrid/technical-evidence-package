# ComfyUI Node V1.0 Decision Record

**Date:** 2026-08-03
**Owner:** Joseph Cruel
**Project:** LucidGrid ComfyUI Provenance Node
**Status:** Decisions 1, 2, and 3 closed. Decisions 4, 5, 6, and 7 are tracked
outside this record.
**Source review:** `comfyui-node/ComfyUI Custom-Node Approaches to AI Provenance for Film & TV`
**Source build prompt:** `comfyui-node/LucidGrid-ComfyUI-Node-Build-Prompt.md`

---

## Framing test applied to every decision below

The node has to earn its install on its own. An artist installs it because it
gives them a readable record of what they did in a session. Model files, LoRAs,
seeds, resolved parameters, the techniques they used, and the outputs those
choices produced. That record is useful the day after a session and useful six
months later when a client asks how a shot was made.

Compliance value rides on top of that. It does not carry the install.

Each decision below answers three questions. Does the choice ship faster. Does
it get adopted faster. Does it cost less to maintain. A choice that only serves
the compliance story and adds build surface gets rejected.

---

## Decision 1: Deployment scope for V1.0

**Decision**

V1.0 supports local and portable ComfyUI installs. Output is a sidecar file
written next to each output asset. No network egress in the default path. Comfy
Cloud, RunComfy, ComfyDeploy, and any remote-API delivery mode move to V1.1.

**Alternatives considered**

Ship local, portable, and the three hosted platforms together in the first
release, with a remote-API delivery mode behind a configurable endpoint.

**Reason for rejection**

The three-week estimate holds because capture is cheap. The review states the
node can already see everything it needs through the `PROMPT`, `EXTRA_PNGINFO`,
and `UNIQUE_ID` hidden inputs (review, Finding E). Reading a graph that is
already in memory and writing a JSON file next to the output is days of work.

Cloud modes do not add capture work. They add a delivery subsystem. Finding E
records the deployment split directly: local and portable installs have full
filesystem access for sidecar writes, while Comfy Cloud, RunComfy, and
ComfyDeploy are sandboxed and egress-limited and therefore need sidecar-to-remote
-API delivery. Studio containers add a third rule, because MPA TPN v5.3.1
governs what may leave the network at all. The build prompt encodes the same
three-way split in its Section 6 table, sets the no-egress default in Section
2.4, and lists cloud and egress modes as out of scope for V1.0 in Section 11.

Attempting that on day one buys four new problems that have nothing to do with
recording a session. An async POST queue with retries and backoff. Endpoint
configuration and override for studio-approved hosts. Egress detection, so the
node behaves correctly when a remote endpoint is configured but unreachable.
Test infrastructure that can simulate three sandboxes the team does not control.
Each of those is a week of its own, and none of them make the local record
better. Three weeks becomes six, and the six-week version ships the same session
record.

There is also an adoption argument. The install base that tries a new node first
is local and portable. That is where the node gets its first hundred users and
its first bug reports. Hosted platforms are where paying studios run, and studios
buy after a reference exists, not before.

**Consequences**

V1.0 runs on local and portable installs. Every output asset gets a
`<stem>.tep.json` sidecar next to it. The node never opens a socket in the
default path, which also removes the security review burden for the first
release.

Sales must qualify deployment context before quoting the node. A prospect running
on Comfy Cloud is a V1.1 prospect.

V1.1 owns the delivery subsystem as a single scoped piece of work, tested against
one hosted platform first rather than three.

**Revisit trigger**

A named customer commits to a deployment date on Comfy Cloud, RunComfy, or
ComfyDeploy. That pulls V1.1 delivery forward. Revisit at V1.1 planning, or
sooner on a signed commitment.

---

## Decision 2: Execution hook strategy

**Decision**

Capture runs in an `OUTPUT_NODE` terminal node. The user places the node at the
end of the graph. It reads the executed prompt through hidden inputs and writes
the record.

**Alternatives considered**

Monkeypatch the ComfyUI executor to install a post-execution callback, so capture
happens for every graph without the user placing a node.

**Reason for rejection**

Version churn is the top maintenance risk on this node. The review says so
directly in Finding E, and names the mitigation, which is pinning
`comfyui-frontend-package` and testing across releases. Finding F reinforces it
from the studio side, where an ILM engineer's account of dependency chaos is the
reason ComfyUI runs in pinned containers at all.

A patched executor turns a bounded problem into an unbounded one. The bounded
version is a fixed contract. `NODE_CLASS_MAPPINGS`, `OUTPUT_NODE`, and the three
hidden inputs are the public custom-node API. Comfy Org keeps them working
because thousands of node packs depend on them. When they change, they change
visibly, and the fix is local to one file.

The unbounded version reaches into execution internals that carry no
compatibility promise. Any release that touches the execution loop can break the
patch, and the break is often silent. The node keeps loading and stops
recording, which is the worst failure mode a record-keeping tool can have. The
maintenance cost is not a one-time port. It is a tax on every upstream release,
forever, and it grows as the executor is refactored. The review notes that mature
packs do resort to patching, which is evidence that it is possible, not evidence
that it is cheap.

Two more reasons, both from the same constraint. First, minimize external
dependencies and external API surface. The `OUTPUT_NODE` path needs nothing
outside the standard library and ComfyUI itself. Second, a patched executor
loads global state at import time, which is exactly what a studio security
reviewer will refuse inside a governed container. The node has to be boring to
approve.

There is one honest cost, and it is a named V1.0 build item.

**Named V1.0 build item, the cost of this choice: multi-output graph handling.**
The terminal node fires once per graph, not once per artifact. A batch that
writes four images has to produce four sidecar records, each sharing one
provenance envelope and each naming its own output artifact and hash. This is
build item 4 below. It is finite, it has a deterministic test, and it is done
once. That is the whole trade. A finite, testable piece of work in exchange for
no ongoing tax.

Cloud delivery modes are deferred for the same reason this decision goes the way
it does. Egress policy handling does not exist in the core ComfyUI node API.
Building it means adding surface that the platform does not support, which is the
same mistake as patching the executor, in a different place.

**Consequences**

The node is a terminal sink. The user has to place it, which means a workflow
without the node produces no record. That is acceptable and arguably correct,
because the record carries a human declaration and a declaration nobody chose to
make is not worth much.

Multi-output handling is a hard requirement for V1.0, not a nice-to-have.

Capture of intermediate, non-terminal state is out of reach until upstream ships
a real hook.

**Revisit trigger**

ComfyUI upstream ships a first-class post-execution or graph-complete callback in
the public API. At that point the terminal node becomes optional and automatic
capture becomes cheap. Revisit at V1.2 planning.

---

## Decision 3: Commoditization trigger, rewritten

**Decision**

Retire the old trigger. The new trigger is any tool shipping a
human-identity-bound attestation with a named signer, where the signer is a
natural person rather than the software vendor.

**Alternatives considered**

Keep the existing trigger, which watches for Comfy Org shipping first-party
provenance or C2PA support.

**Reason for rejection**

The old trigger watches the wrong layer. Native capture at the platform layer is
an input to the declaration layer, not a substitute for it. A platform that
records model, parameters, seed, and timestamp has recorded what the software
did. It has not recorded what a person decided. Those are different claims, and
only the second one supports a human-authorship position or an editorial-control
position.

The review's own evidence shows the gap. ComfyUI already writes prompt and
workflow JSON into PNG `tEXt` chunks today (Finding C). That is native capture,
shipping, in every install. It did not commoditize anything, because the metadata
dies on the first re-encode and carries no signer. Genvid captures model,
parameters, prompt, user, and timestamp bound to a production object at
generation time (Finding G), which is the strongest capture in the market, and it
still does not bind a named natural person to a creative decision. OMC's
Provenance construct is free-text who, when, and why with no human-versus-machine
schema (Finding, Claim 5).

If the old trigger had fired on any of those, the response would have been a
pivot away from a position nobody else occupies. That is the failure mode of a
badly aimed trigger. It burns the pivot early.

The rewritten trigger fires on the thing that actually competes. A named natural
person, cryptographically bound, attesting to their own creative decisions.

**Vendors to monitor**

1. **Comfy Org.** Release notes and `blog.comfy.org`. Registry announcements.
2. **Genvid.** Product pages and release notes for extension past
   user-attribution into signer identity.
3. **Digital Domain DDAI.** Public statements from Matt Smith and DDAI product
   material, which today reads as vendor and model governance.
4. **Adobe Content Authenticity.** CAWG identity assertion rollout, and whether
   it extends from asset-level signing into per-decision attestation.
5. **Comfy-Cozy** (`JosephOIbrahim/Comfy-Cozy`). Ships `workflow.lock` with model
   SHA-256 pinning today. Watch the repo for movement toward named-signer
   declarations.

**Artifacts that constitute the trigger firing**

1. A C2PA manifest on a ComfyUI-produced asset carrying an AI-disclosure
   assertion with a human-oversight value, together with a CAWG identity
   assertion binding the signer to a natural person's credential.
2. A ComfyUI node pack or first-party feature that emits a human-authorship
   declaration with a named human signer by default, not as an optional field.
3. An OMC v3.0 release that adds a human-identity-bound attestation schema, plus
   a shipping ComfyUI-native implementation of it. The schema alone does not fire
   the trigger. The schema plus an implementation does.
4. A studio platform, Genvid or DDAI, publishing a per-session declaration record
   signed by a named artist rather than by the platform.

Any one artifact fires the trigger.

**Monitoring cost, and why it stays low**

This is a weekly scan, not an engineering project. Thirty minutes. Five release
feeds and one registry search. It goes on one person's
calendar and produces a one-line note. If it ever needs a dashboard or a
crawler, the scope has drifted and should be cut back.

**The response is a pre-built pivot, not a new build**

The pivot is written down before the trigger fires, so firing costs days and not
a quarter. When a platform ships bound human identity, the node stops being a
capture tool and becomes a thin exporter that reads the platform's attestation
and emits it in TEP format alongside the session record. The capture code becomes
a fallback path for installs the platform does not cover. The declaration schema,
the sidecar format, the validator, and the offline reader all survive unchanged,
because they were never coupled to who did the capturing. That decoupling is the
pivot, and it is already how the V1.0 build is scoped.

**Consequences**

No engineering work is triggered by monitoring. The build proceeds as specified.
The weekly scan is an owner obligation, not a team obligation.

If the trigger fires, the response is a scoping conversation against a written
pivot, not a rearchitecture.

**Revisit trigger**

Weekly scan of the five vendors. Escalate the same week on any trigger artifact.
Re-examine the trigger definition itself at V1.1 planning, in case the market has
moved somewhere none of the four artifacts describe.

---


## V1.0 build list

Estimates are in working days for one engineer. Items are ordered by dependency.

| # | Item | Estimate | Flag |
|---|---|---|---|
| 1 | Package skeleton, node registration, `NODE_CLASS_MAPPINGS`, category `LucidGrid/Provenance` | 0.5 | |
| 2 | `OUTPUT_NODE` capture node reading `PROMPT`, `EXTRA_PNGINFO`, `UNIQUE_ID` | 1.0 | |
| 3 | Parameter and seed extraction from the executed prompt, post-resolution | 1.0 | |
| 4 | Multi-output handling, one sidecar per output artifact, shared envelope | 1.5 | Cost of Decision 2 |
| 5 | Lazy cached SHA-256 hasher keyed on path, mtime, size, for models and outputs | 0.75 | |
| 6 | Sidecar writer, atomic write via temp file plus `os.replace` | 1.0 | |
| 7 | Declaration fields on the node, declarant, role, intent, schema version | 1.0 | |
| 8 | Technique tag detector from node-type taxonomy | 1.0 | |
| 9 | Schema definition plus stdlib validator, no new dependency | 1.0 | |
| 10 | Failure-mode handling, missing `EXTRA_PNGINFO`, missing model file, unwritable path | 0.75 | |
| 11 | Node version drift record, installed versus recorded | 0.5 | |
| 12 | Registry packaging, `pyproject.toml`, `[tool.comfy]`, `.comfyignore`, frontend package pin | 0.5 | |
| 13 | Registry publish GitHub Action, committed but not executed | 0.25 | **Gated on an external clearance tracked outside this record** |
| 14 | Example workflow JSON plus example emitted sidecar | 0.5 | |
| 15 | Tests, schema round-trip, hasher cache, multi-output, seed resolution, no-network | 1.5 | |
| 16 | README, install, usage, field glossary | 0.5 | |
| 17 | Cross-release testing on three ComfyUI versions | 1.5 | Estimate depends on release cadence |
| 18 | OMC v2.8-compatible JSON mapper, `omc_compatible_json` block, runs after item 9 | 2.0 | Estimate depends on OMC v3.0 timing |

**Total: 18.25 days.**

Three and a half weeks of build plus a day and a half of cross-release hardening.
The hardening is not optional, because Decision 2 named version churn as the top
maintenance risk and this is the mitigation.

The OMC mapper is in V1.0 by owner decision, against the recommendation below.
That is a two-day add on a three-week target, so the schedule is now three and a
half weeks and the buffer is gone.

**Items where the estimate depends on an unresolved question**

- **Item 13** is gated, not estimated away. The work is a quarter day. Whether it
  ever runs depends on an external clearance tracked outside this record. If that
  clearance does not come, the item stays committed and unused, and distribution
  runs direct to customers under agreement rather than through the registry.
- **Item 17** assumes three ComfyUI releases within the test window that do not
  break the custom-node API. A breaking release inside the window adds one to two
  days. The review flags version churn as the top risk, so this estimate is the
  least certain number on the list.
- **Item 8** assumes the technique taxonomy stays at the node classes named in
  the build prompt. Each additional family of detectors adds roughly a quarter
  day. The list should stay short for V1.0 and grow from user requests.
- **Item 18** assumes the mapper targets OMC v2.8 only. OMC v3.0 lands later in
  2026 and MovieLabs describes it as expanding into the full video pipeline. If
  v3.0 publishes before V1.0 ships, the mapper needs a rewrite or a version
  branch, which adds one to two days. This is the second least certain number on
  the list after item 17.

**Owner override on the OMC mapper**

The recommendation was to cut the mapper to V1.1. The owner kept it in V1.0. It
stays in, as item 18. Recorded rationale for the override is build prompt Section
10 item 4, which lists OMC-compatible JSON as a V1.0 GTM requirement, and the
review's Recommendation 3, which positions LucidGrid as a complement to OMC,
Genvid, and ShotGrid rather than a competitor.

The recommendation against it stands on the record. The mapper serves the buyer
conversation, not the artist reading a session record. It imports an external
schema and its release cadence into a V1.0 with a hard date. It is the one item
on the list most likely to need rework within months of shipping.

Two guardrails on the item, both cheap. Keep the mapper behind a single module
with no other module importing OMC vocabulary, so a v3.0 rewrite touches one
file. Make the block emission a node toggle defaulting to on, so a broken mapper
never blocks a session record from being written.

**Items removed from the previous build list, and why**

- **OpenTimestamps anchoring, 1 day.** Cut to V1.1. Anchoring serves the evidence
  claim, not the session record. It adds a network dependency to a release whose
  whole scope decision was no egress by default, and Decision 1 removed exactly
  that kind of surface. The sidecar records a local timestamp and a content hash,
  which is what the record needs to be useful. Anchoring is a service-side
  operation that can run over any sidecar after the fact, which is the correct
  place for it and also the part LucidGrid keeps proprietary.
- **C2PA fingerprint field, 0.25 days.** Cut. It detects whether a C2PA signer
  node sits in the graph and records a fingerprint. Nobody asked for it, and the
  review already establishes that ComfyUI's own SaveImage strips C2PA
  signatures. Speculative field, no user.

The two cuts free 1.25 days. Multi-output handling and cross-release testing are
funded first, because they decide whether the node works in the installs it ships
to. With the mapper back in, the release runs 3.25 days over the original
three-week target and carries no schedule buffer.

---

## Action register

| Action | Owner | Due | Status |
|---|---|---|---|
| Weekly commoditization scan, five vendors | Joseph Cruel | Weekly | Open |
| Resolve registry-publish clearance before item 13 runs | Joseph Cruel | Tracked outside this record | Open |

## Decisions not closed here

- **Decision 4, OMC and Genvid relationship.** The build half is settled. V1.0
  emits an OMC v2.8-compatible block, item 18. The positioning half, how
  LucidGrid presents itself against Genvid in a sales conversation, is not closed
  here.
- **Decision 5, anchoring default.** Deferred by the anchoring cut above. Reopen
  at V1.1 with the TPN no-egress question attached.
- **Decision 6, distribution clearance.** Tracked outside this record. It gates
  item 13 and nothing else in the build list.
- **Decision 7, adoption gate.** Needs a date and a named-production evidence
  bar. Not closed here.
