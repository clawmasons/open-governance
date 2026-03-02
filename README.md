# Open Governance Protocol

*Constitutional governance for the age of AI agents.*

## The Problem

Locke saw it in 1689. Unchecked power gets abused. It doesn't matter whether the sovereign is benevolent. Convenience and self-interest are universal. The solution was never better kings. It was constitutions, separation of powers, rule of law.

The same problem is back.

Anyone with an API key can deploy an AI agent that reads your data, sends emails on your behalf, moves money, and makes decisions about people — at machine speed, around the clock. A jailbroken open-weight model has the same tool-calling power as a carefully governed enterprise agent. The MCP endpoint that processes your payroll can't tell the difference.

Current defenses ask the model to govern itself. Safety training. System prompts. Alignment research. These matter — but they are the equivalent of asking the king to be virtuous. Safety training can be bypassed. System prompts can be jailbroken. Open-weight models can have their guardrails removed entirely. Governance inside the model is governance the operator can ignore.

## The Solution

The Enlightenment thinkers didn't build better monarchs. They built systems that constrained power structurally:

- **Locke**: Legitimate authority requires consent and must protect natural rights. Power that violates rights is illegitimate, regardless of source.
- **Montesquieu**: Separate power so no single actor can legislate, execute, and judge.
- **Rousseau**: Governance derives authority from a social contract — explicit, documented agreement.

This protocol applies those principles to AI agent tool execution.

An organization writes a **constitution** — not a system prompt, but an on-chain governance document registered via [Ethereum Attestation Service (EAS)](https://attest.org). It defines protected rights, prohibited actions, and the rules agents must follow. Rousseau's social contract, made cryptographically verifiable.

That constitution produces **laws** — enforceable rules as separate attestations. The authority that writes the laws is not the agent that executes them, and neither is the system that audits them. Montesquieu's separation of powers.

Every skill, prompt, and workflow must be **certified** against those laws before production. Every tool call is **signed** by the governance framework. High-risk actions require a **governance warrant** — signed human authorization, just as a property search requires judicial approval.

The tool endpoint checks the signature. Verified governance structure? Execute. No governance? Reject. **The model doesn't matter.** GPT, Claude, Llama, a jailbroken open-weight model — governance is enforced at the boundary, not inside the mind.

Locke's insight, implemented as infrastructure: rights are protected by the structure of the system, not the virtue of those in power.

## How It Works

```
Constitution → Laws → Certified Skills → Signed Requests → Execution Audits
     ↑                                                            ↑
  on-chain                                              tamper-proof
  (EAS)                                              (Merkle anchored)
```

**Setup:** The tool vendor verifies the org's on-chain governance and issues client credentials — like OAuth, but backed by a verifiable constitutional model.

**Runtime:** Each tool call carries a cryptographic signature from the governance framework. The vendor checks the signature, the credential, and the certification. Three checks, sub-millisecond. No LLM inference on the hot path.

**Audit:** Merkle batch anchoring. Tamper-proof trail. Both parties maintain independent records, anchored on-chain. Neither can deny or alter what happened.

## Architecture

```
GOVERNANCE LAYER (the constitution and the laws)
  Governance Framework      — Constitution, bill of rights, laws, policies
  Governed Registry         — Certified skills, prompts, commands, subagents

RUNTIME LAYER (the executive, constrained by law)
  Governed Agent Container  — Locked-down execution environment
  Governed Tools Proxy      — Signs, validates, forwards/rejects tool calls
  Governance Service        — Issues governance warrants for high-risk actions

ACCOUNTABILITY LAYER (the judiciary and the public record)
  Governance Ledger         — Attestation chain verification, audit, compliance
```

## Rights-Based Risk Classification

Not all exercises of power are equally dangerous. Reading a Slack channel is different from initiating a wire transfer.

Risk levels derive from which fundamental rights an action could impact:

| Right | Examples | Default Floor |
|---|---|---|
| Life & Safety | Medical systems, infrastructure control | CRITICAL |
| Liberty & Autonomy | Hiring decisions, content moderation | HIGH |
| Property | Financial transactions, data deletion | Scales with value |
| Privacy & Dignity | PII access, data exports | Scales with sensitivity |
| Due Process | Automated decisions about people | HIGH |
| Consent | Acting on someone's behalf | Scales with authorization |
| Cognitive Integrity | Memory modification or deletion | HIGH / CRITICAL |

Risk classification determines the execution model — from schema validation (80–90% of requests, zero cost) to deterministic execution with mandatory human approval (critical actions only).

## Governance Warrants

When an agent needs to take an action that could impact someone's rights, it cannot simply proceed. It must obtain a **governance warrant**: a signed authorization from a designated Warrant Authority.

The parallel is exact. Law enforcement cannot search your property without a warrant from an independent authority who reviewed the justification. An AI agent cannot take a rights-impacting action without the same.

The warrant is attested on-chain. Permanent record. Auditable, appealable, accountable.

## Cognitive Integrity

The Enlightenment thinkers assumed the mind was inviolable. That assumption is becoming obsolete.

This protocol introduces **Cognitive Integrity** as a protected right: the contents of a mind — human or artificial — may be governed and access-controlled, but never destroyed. Sensitive data may be restricted. The record that it existed must persist.

The principle: **govern access, preserve existence.**

If an agent's memory can be selectively deleted, the governance stack is unreliable — memory is evidence. As neurotechnology advances, the same principle applies to human cognitive data. The infrastructure built for AI agents today may become the template for cognitive rights governance broadly.

## Specification

See **[RFC.md](./RFC.md)** for the full protocol specification, including EAS attestation schemas, the risk classification function, execution models, validation architecture, Merkle batch anchoring, tamper resistance, and on-chain contract specification.

## Status

This protocol is in draft stage. The [RFC](./RFC.md) is open for review and comment.

## License

MIT
