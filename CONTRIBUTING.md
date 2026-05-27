# Contributing

Two pathways.

---

## 1. Contributing a tool detector to the registry

The tool registry is an open resource. Any platform or individual can contribute a detector for an AI tool not yet covered. Contributing a detector does not require commercial engagement with LucidGrid.

Read the full process: [`registry/CONTRIBUTING_DETECTORS.md`](registry/CONTRIBUTING_DETECTORS.md).

The short version:
- Add a JSON file to `registry/tools/` conforming to the detector schema.
- Open a PR with a verification note confirming the URL patterns were tested against a live session.
- Include the indemnification status source URL.
- Target review time: five business days.

---

## 2. Contributing to the specification

Specification changes follow an RFC-style process.

**Requirements for a spec change PR:**

1. A clear statement of the problem the change solves — what evidence gap, what interoperability requirement, what legal context.
2. A proposed change to the affected `spec/` or `schema/` files with tracked changes.
3. A working example TEP (or a diff against an existing example in `examples/`) that demonstrates the change in practice.
4. A note on backward compatibility: does the change break existing v1.x TEPs? If yes, it requires a major version bump and explicit migration guidance.

**Tone.** This is a practitioner-to-practitioner specification. Changes are evaluated on whether they improve the evidentiary quality, interoperability, or legibility of the format. Marketing language, competitive framing, and vendor-specific requirements that do not generalize across the industry will not be merged.

**Review timeline.** Major spec changes (new evidence types, new capability levels, schema breaking changes) require a 30-day comment period. Clarifications and additions to existing spec language can move faster.

---

## What is in scope

- New tool detectors
- Corrections to existing tool registry entries (indemnification status changes, URL pattern updates)
- Clarifications to spec language
- New optional fields that do not break existing conformant TEPs
- New worked examples

## What is out of scope

- Changes that weaken the five conditions for valid authorship evidence
- Changes that remove or rename the three evidence types
- Vendor-specific extensions that do not generalize
- Legal opinions embedded in spec language

---

Questions: open an issue before writing a PR for any spec change.
