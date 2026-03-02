# Open Governance Protocol

A model-agnostic governance protocol for AI agent tool execution. Enforce constitutional governance over MCP/API calls using on-chain attestation chains, regardless of which AI model generates the request.

## The Problem

Anyone can spin up an LLM — including jailbroken open-weight models with no safety guardrails — and point it at production tools. Current governance relies on model-level safety training, which is bypassable. There is no standard way for tool endpoints to verify that an AI agent operates under a defined governance framework.

## The Solution

Move governance enforcement to the tool execution boundary. An organization defines its constitution, bill of rights, and laws on-chain via [Ethereum Attestation Service (EAS)](https://attest.org). All components driving an agent — skills, prompts, workflows — are pre-certified against those laws. At runtime, a governance framework validates every request and signs it with a cryptographic attestation. The tool vendor verifies the signature and checks that it comes from an organization with a recognized on-chain governance structure.

**The model doesn't matter.** GPT, Claude, Llama, a jailbroken open-weight model — if the request isn't signed by a recognized governance framework operating under a ratified constitution, the endpoint rejects it.

## How It Works

```
Constitution → Laws → Certified Skills → Signed Requests → Execution Audits
     ↑                                                            ↑
  on-chain                                              tamper-proof
  (EAS)                                              (Merkle anchored)
```

**Setup:** The tool vendor verifies the org's on-chain governance and issues client credentials — like OAuth, but backed by a verifiable constitutional model.

**Runtime:** Each tool call carries a cryptographic signature from the governance framework. The vendor checks the signature. Three checks, sub-millisecond.

**Audit:** Merkle batch anchoring gives a tamper-proof audit trail. Both parties maintain independent records, anchored on-chain.

## Architecture

```
GOVERNANCE LAYER
  Governance Framework      — Constitution, bill of rights, laws, policies
  Governed Registry         — Certified skills, prompts, commands, subagents

RUNTIME LAYER
  Governed Agent Container  — Locked-down execution environment
  Governed Tools Proxy      — Signs, validates, forwards/rejects tool calls
  Governance Service        — Governance warrants for high-risk actions

ACCOUNTABILITY LAYER
  Governance Ledger         — Attestation chain verification, audit, compliance
```

## Rights-Based Risk Classification

Risk levels aren't arbitrary — they derive from which fundamental rights an action could impact:

| Right | Examples | Default Floor |
|---|---|---|
| Life & Safety | Medical systems, infrastructure control | CRITICAL |
| Liberty & Autonomy | Hiring decisions, content moderation | HIGH |
| Property | Financial transactions, data deletion | Scales with value |
| Privacy & Dignity | PII access, data exports | Scales with sensitivity |
| Due Process | Automated decisions about people | HIGH |
| Consent | Acting on someone's behalf | Scales with authorization |
| Cognitive Integrity | Memory modification or deletion | HIGH / CRITICAL |

Risk classification determines the execution model — from simple schema validation (80-90% of requests, zero cost) to deterministic execution with human approval (critical actions only).

## Governance Warrants

High-risk and critical-risk actions require a **Governance Warrant** — a signed authorization from a designated Warrant Authority (human or governed AI agent). Like a legal warrant: the agent presents justification, the authority reviews the rights impact, and the signed approval becomes part of the on-chain attestation chain.

## Specification

See [RFC.md](./RFC.md) for the full protocol specification, including EAS attestation schemas, the risk classification function, task-level governance, tamper resistance architecture, and on-chain implementation details.

## GRC Alignment

The protocol maps to established Governance, Risk, and Compliance categories:

| GRC Standard | Protocol Component |
|---|---|
| Policy Management | Governance Framework |
| Control Framework | Governed Registry |
| Risk Assessment | Rights-Based Risk Classification |
| Compliance Monitoring | Governed Agent Container |
| Control Enforcement | Governed Tools Proxy |
| Issue Management | Governance Service |
| Audit Management | Governance Ledger |

## Status

This protocol is in draft stage. The [RFC](./RFC.md) is open for review and comment.

## License

MIT
