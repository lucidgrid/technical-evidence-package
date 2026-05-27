# Security Policy

## Scope

This repository contains specification documents, JSON schemas, and example data. It does not contain executable code.

Security disclosures are relevant to:

- **Schema vulnerabilities** — fields or structures in `manifest.schema.json` or `metadata.schema.json` that could be exploited to produce a non-conformant TEP that passes validation (false-positive conformance).
- **Example data** — any private or sensitive data inadvertently included in `examples/`.
- **Specification ambiguities** — wording in `spec/` documents that could be interpreted to weaken the evidentiary chain in a way not intended by the constitution.

## Reporting

Report security issues to **hello@lucidgrid.tech**. Do not open a public GitHub issue for security disclosures.

Include:
- A description of the issue
- Steps to reproduce (if applicable)
- Your assessment of impact

We will acknowledge receipt within five business days and aim to resolve confirmed issues within 30 days.

## Out of Scope

- Vulnerabilities in third-party tools or services referenced in this specification (Supabase, OpenTimestamps, etc.) — report those to the respective vendors.
- Theoretical attacks on the evidentiary model — open a public issue or discussion instead; these are specification design questions, not security vulnerabilities.
