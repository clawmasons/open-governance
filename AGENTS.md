# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **specification-only repository** for the Open Governance Protocol — a model-agnostic governance protocol for AI agent tool execution. It contains no application code, build system, or tests.

The core artifact is **RFC.md**, an IETF-style Internet-Draft (`draft-clawforge-open-governance-00`) specifying how to enforce constitutional governance over MCP/API calls using on-chain attestation chains (Ethereum Attestation Service). The README.md provides a high-level summary.

## Key Files

- `RFC.md` — Full protocol specification (~1400 lines). This is the primary working document.
- `README.md` — Public-facing summary of the protocol.

## Protocol Architecture (for context when editing)

The protocol defines six component roles across three layers:

- **Governance Layer**: Governance Framework (constitution/laws on-chain) + Governed Registry (certified skills/prompts)
- **Runtime Layer**: Governed Agent Container (execution sandbox) + Governed Tools Proxy (signs/validates calls) + Governance Service (warrants for high-risk actions)
- **Accountability Layer**: Governance Ledger (attestation chain, Merkle-anchored audit)

Seven EAS attestation schemas form a hierarchical chain: Constitution → Bill of Rights → Law → Certification → Session → Execution → Audit.

Risk classification is rights-based (Life & Safety, Liberty, Property, Privacy, Due Process, Consent, Cognitive Integrity) and determines validation tiers from schema-only (low risk) to deterministic execution with human approval (critical risk).

## Writing Conventions

The RFC uses IETF formatting conventions (RFC 2119 keywords: MUST, SHOULD, MAY). When editing RFC.md, maintain the fixed-width text formatting style with 3-space indentation and ~72-character line width used throughout.
