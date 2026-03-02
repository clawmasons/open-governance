
Internet-Draft                                                D. Greff
Intended status: Standards Track                            Clawmasons
Expires: September 2, 2026                              March 2, 2026


         Open Governance Protocol for AI Agent Tool Execution
                   draft-clawforge-open-governance-00

Abstract

   This document specifies the Open Governance Protocol, a framework
   for enforcing constitutional governance over AI agent interactions
   with external tools and APIs. The protocol uses Ethereum Attestation
   Service (EAS) to create verifiable, on-chain attestation chains that
   link organizational governance constitutions to individual AI agent
   tool calls.

   The protocol is model-agnostic: governance is enforced at the tool
   execution boundary, not within the AI model, ensuring that even
   compromised or jailbroken models cannot bypass governance controls.

   The protocol defines six component roles (Governance Framework,
   Governed Registry, Governed Agent Container, Governed Tools Proxy,
   Governance Service, and Governance Ledger), seven EAS attestation
   schemas forming a hierarchical chain of authority, a rights-based
   risk classification framework derived from natural rights
   philosophy, and a tiered validation architecture that balances
   governance rigor with operational cost.

Status of This Memo

   This Internet-Draft is submitted in full conformance with the
   provisions of BCP 78 and BCP 79.

   Internet-Drafts are working documents of the Internet Engineering
   Task Force (IETF). Note that other groups may also distribute
   working documents as Internet-Drafts.

   This Internet-Draft is valid for a maximum of six months and may be
   updated, replaced, or obsoleted by other documents at any time.

Copyright Notice

   Copyright (c) 2026 ClawForge and the persons identified as the
   document authors. All rights reserved.

Table of Contents

   1.  Introduction  . . . . . . . . . . . . . . . . . . . . . . .  2
   2.  Conventions and Definitions . . . . . . . . . . . . . . . .  3
   3.  Protocol Overview . . . . . . . . . . . . . . . . . . . . .  4
   4.  Attestation Schema Definitions  . . . . . . . . . . . . . .  6
   5.  Rights-Based Risk Classification  . . . . . . . . . . . . . 14
   6.  Task-Level Governance . . . . . . . . . . . . . . . . . . . 21
   7.  Tiered Validation Architecture  . . . . . . . . . . . . . . 25
   8.  Request Flow  . . . . . . . . . . . . . . . . . . . . . . . 28
   9.  Off-Chain Attestation and Tamper Resistance . . . . . . . . 30
   10. Component Architecture  . . . . . . . . . . . . . . . . . . 35
   11. On-Chain Implementation . . . . . . . . . . . . . . . . . . 42
   12. Security Considerations . . . . . . . . . . . . . . . . . . 44
   13. Privacy Considerations  . . . . . . . . . . . . . . . . . . 47
   14. Cost Analysis . . . . . . . . . . . . . . . . . . . . . . . 48
   15. IANA Considerations . . . . . . . . . . . . . . . . . . . . 49
   16. References  . . . . . . . . . . . . . . . . . . . . . . . . 49
   Appendix A. Governance Mapping to GRC Standards . . . . . . . . 50
   Appendix B. Philosophical Foundations . . . . . . . . . . . . . 51
   Authors' Addresses  . . . . . . . . . . . . . . . . . . . . . . 52


1.  Introduction

1.1.  Problem Statement

   The proliferation of AI agents capable of executing tool calls via
   protocols such as the Model Context Protocol (MCP) [MCP] creates an
   urgent governance challenge. Any actor can deploy an AI model —
   including open-weight models with safety controls removed — and
   direct it to interact with production tools and APIs. Current
   governance approaches rely on model-level safety training, which is
   bypassable, or platform-level access controls, which lack
   constitutional structure and auditability.

   Specific problems this protocol addresses:

   a) Access Creep: AI agents accumulate permissions over time,
      operating with broader access than their current task requires.

   b) Ungoverned Execution: No standardized mechanism exists for tool
      endpoints to verify that an AI agent operates under a defined
      governance framework.

   c) Audit Gaps: Existing systems cannot produce tamper-proof records
      linking organizational policy to individual agent actions.

   d) Model Dependence: Governance enforced within the model (system
      prompts, RLHF) can be bypassed by model replacement, jailbreaking,
      or prompt injection.

1.2.  Design Goals

   The Open Governance Protocol is designed to achieve the following:

   a) Model Agnosticism: Governance MUST be enforced regardless of
      which AI model generates the tool call.

   b) Constitutional Structure: Organizations MUST be able to define
      hierarchical governance — constitutions, rights, laws, and
      policies — with verifiable chain of authority.

   c) Boundary Enforcement: Governance MUST be enforced at the tool
      execution boundary, not within the model.

   d) Minimal Runtime Cost: Per-request governance validation MUST
      be achievable without per-request LLM inference.

   e) Tamper-Proof Auditability: The complete chain from
      organizational policy to individual action MUST be
      cryptographically verifiable.

   f) Rights-Based Risk Classification: The risk level of any agent
      action MUST derive from which fundamental rights it could impact.

   g) Proportional Verification: The rigor of execution verification
      MUST scale with the risk classification of the action.

1.3.  Scope

   This protocol specifies:

   - Attestation schemas for governance documents, policies,
     credentials, certifications, requests, warrants, and audits.
   - A rights-based risk classification framework.
   - Task-level governance with risk-proportional execution models.
   - Off-chain attestation with on-chain Merkle anchoring for tamper
     resistance.
   - Component roles and their interactions.

   This protocol does not specify:

   - The internal architecture of AI models.
   - The implementation details of MCP or other tool protocols.
   - Specific organizational governance policies.
   - Blockchain consensus mechanisms or EAS internals.


2.  Conventions and Definitions

   The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
   "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY",
   and "OPTIONAL" in this document are to be interpreted as described
   in BCP 14 [RFC2119] [RFC8174] when, and only when, they appear in
   all capitals, as shown here.

   The following terms are used throughout this document:

   Attestation: A cryptographically signed data structure registered
   via the Ethereum Attestation Service (EAS), containing structured
   data and referencing a defined schema.

   Constitution: The foundational governance document registered
   on-chain by an organization, defining core rights, prohibited
   actions, and amendment rules for AI agent operations.

   Governance Framework: A system implementing this protocol that
   validates agent requests against organizational governance and
   issues credentials. ClawForge is an example implementation.

   Governed Registry: A certified component store operating under a
   Governance Framework's authority, containing validated skills,
   prompts, commands, and subagent definitions.

   Governed Agent Container: A locked-down execution environment for
   AI agents that enforces governance by controlling input/output
   and restricting network access to the Governed Tools Proxy.

   Governed Tools Proxy: A boundary enforcement component that
   validates, signs, and forwards governed tool calls to external
   MCP/API endpoints.

   Governance Service: The component managing the governance warrant
   process — receiving requests, routing to Warrant Authorities,
   validating constitutional compliance, and handling appeals.

   Governance Ledger: The accountability component providing
   attestation chain verification, Merkle anchor validation, anomaly
   detection, and compliance reporting.

   Governance Warrant: A signed authorization issued by a Warrant
   Authority permitting an AI agent to execute a high-risk or
   critical-risk action.

   MCP: Model Context Protocol, a protocol enabling AI models to
   interact with external tools and data sources.

   Rights Category: A classification of fundamental rights that may
   be impacted by an AI agent action, used as input to the risk
   classification function.

   Risk Classification: A level (LOW, MEDIUM, HIGH, CRITICAL)
   assigned to a task based on which rights it could impact and
   contextual factors such as reversibility and scope.

   Skill: A certified, bounded capability registered in the Governed
   Registry, containing one or more task definitions.

   Task Definition: A bounded operation within a skill, specifying
   the exact MCP tool, parameter schema, risk classification, and
   execution model.

   Warrant Authority: A human or governed AI agent authorized to
   review and issue Governance Warrants for high-risk and
   critical-risk actions.


3.  Protocol Overview

3.1.  Attestation Chain

   The protocol defines a hierarchical chain of EAS attestations.
   Each layer references its parent via attestation UID, forming a
   verifiable chain of authority from organizational constitution to
   individual tool call.

       OrgConstitution (Layer 1 - root)
           |
           +-- GovernancePolicy (Layer 2 - laws)
                   |
                   +-- SkillCertification (Layer 3 - certified components)
                   |
                   +-- AgentCredential (Layer 4 - just-in-time credentials)
                           |
                           +-- GovernanceWarrant (Layer 5 - high-risk authorization)
                           |
                           +-- MCPRequestAttestation (Layer 6 - per-call signing)
                                   |
                                   +-- ExecutionAudit (Layer 7 - post-execution record)

   If any attestation in the chain is revoked, everything below it
   becomes invalid. Revoking a policy invalidates all credentials
   and skill certifications referencing it. Revoking a constitution
   invalidates the entire governance tree.

3.2.  Trust Model

   The protocol separates trust establishment from request-time
   verification:

   a) Setup Time: The tool vendor verifies the organization's on-chain
      governance structure (constitution, policies, recognized
      governance framework) and issues client credentials. This is
      analogous to OAuth client registration but with verifiable
      governance as the basis for trust.

   b) Request Time: Each tool call carries a cryptographic signature
      from the governance framework. The vendor verifies the signature
      against the framework's on-chain public key and checks credential
      validity. Three checks, sub-millisecond.

   c) Audit Time: Merkle batch anchoring provides a tamper-proof audit
      log. Both the governance framework and the tool vendor maintain
      independent records, anchored on-chain. This is accountability
      infrastructure, not enforcement infrastructure.

3.3.  Component Roles

   The protocol defines six component roles organized across three
   layers:

   Governance Layer (build-time):
     - Governance Framework: Constitution, rights, laws, policies
     - Governed Registry: Certified skills, prompts, commands

   Runtime Layer (request-time):
     - Governed Agent Container: Locked execution environment
     - Governed Tools Proxy: Boundary enforcement, signing
     - Governance Service: Warrants, routing, appeals

   Accountability Layer (audit-time):
     - Governance Ledger: Attestation chains, audit, compliance


4.  Attestation Schema Definitions

   This section defines the EAS schemas used by the protocol. All
   schemas MUST be registered on EAS and referenced by their schema
   UID. Implementations MAY extend schemas with additional fields
   but MUST include all fields marked as REQUIRED.

4.1.  OrgConstitution Schema

   The root attestation in the governance chain. An organization
   MUST register exactly one active OrgConstitution attestation at
   any time. Amendments create new attestations and revoke the prior
   version.

   Field                 Type       Req   Description
   -------------------------------------------------------------------
   orgId                 bytes32    R     Unique organization identifier
   orgName               string     R     Human-readable org name
   constitutionURI       string     R     IPFS URI of full constitution
   constitutionHash      bytes32    R     SHA-256 of constitution document
   version               string     R     Semantic version (e.g., "1.0.0")
   ratifiedAt            uint256    R     Timestamp of ratification
   governanceAuthority   address    R     Multisig or DAO address with
                                         amendment authority
   coreRights            string[]   R     Rights categories recognized
                                         (see Section 5)
   prohibitedActions     string[]   R     Universally prohibited actions
   amendmentThreshold    uint256    O     Votes required for amendment
   rightsFramework       bytes      R     Encoded RightsFramework
                                         (see Section 5.3)

   The constitutionURI MUST point to an immutable content-addressed
   store (e.g., IPFS). The constitutionHash MUST be the SHA-256 hash
   of the document at that URI, enabling integrity verification
   without fetching the full document.

4.2.  GovernancePolicy Schema

   Specific enforceable rules derived from the constitution. Each
   policy attestation MUST reference the parent OrgConstitution via
   its attestation UID.

   Field                 Type       Req   Description
   -------------------------------------------------------------------
   policyId              bytes32    R     Unique policy identifier
   constitutionUID       bytes32    R     References parent OrgConstitution
   policyName            string     R     Human-readable name
   policyType            string     R     Category: "data_access" |
                                         "financial" | "communication" |
                                         "compliance" | "operational"
   policyURI             string     R     IPFS URI of detailed policy
   policyHash            bytes32    R     SHA-256 of policy document
   allowedActions        string[]   R     Permitted actions under this policy
   deniedActions         string[]   R     Prohibited actions
   financialLimit        uint256    O     Max transaction value in wei
   dataClassification    string     R     "public" | "internal" |
                                         "confidential" | "restricted"
   requiresHumanApproval bool       O     Escalation flag
   expiresAt             uint256    R     Policy sunset timestamp

   Policies MUST have expiration dates to force periodic review.
   Policies MAY be independently revoked without affecting the
   constitution or sibling policies.

4.3.  SkillCertification Schema

   Attests that a skill (a set of bounded task definitions) has been
   validated against the organization's constitution and policies
   at build time. This is the pre-runtime certification gate.

   Field                 Type       Req   Description
   -------------------------------------------------------------------
   certificationId       bytes32    R     Unique certification identifier
   skillId               bytes32    R     Unique skill identifier
   skillVersion          string     R     Skill version
   skillHash             bytes32    R     Hash of skill definition
   constitutionUID       bytes32    R     References root constitution
   policyUIDs            bytes32[]  R     All applicable policy UIDs
   certifiedBy           address    R     Certifying entity address
   certificationMethod   string     R     "llm_review" | "manual" |
                                         "automated"
   toolAccessPatterns    string[]   R     MCP tools this skill accesses
   dataClassifications   string[]   R     Data classification levels
   promptTemplateHashes  bytes32[]  R     Hashes of prompt templates
   taskDefinitions       bytes      R     Encoded TaskDefinition array
                                         (see Section 6)
   compliant             bool       R     Certification result
   conditions            string[]   O     Conditions on certification
   restrictions          string[]   O     Usage restrictions
   riskScore             uint256    R     Computed risk score
   certifiedAt           uint256    R     Timestamp
   validUntil            uint256    R     Certification expiry
   policyVersionsHash    bytes32    R     Hash of policy versions at
                                         certification time

   Implementations MUST re-certify skills when the policyVersionsHash
   no longer matches current policy versions. This ensures that policy
   changes cascade to skill certifications.

4.4.  AgentCredential Schema

   A just-in-time, minimally-scoped credential issued to an AI agent
   for a bounded session. Credentials MUST be short-lived.

   Field                 Type       Req   Description
   -------------------------------------------------------------------
   credentialId          bytes32    R     Unique credential identifier
   agentId               bytes32    R     The AI agent receiving credential
   policyUIDs            bytes32[]  R     Governing policy UIDs
   issuedBy              address    R     Governance framework address
   roleName              string     R     Agent role for this session
   authorizedTools       string[]   R     Permitted MCP tools
   authorizedActions     string[]   R     Permitted actions per tool
   authorizedResources   string[]   R     Permitted resource scopes
   validFrom             uint256    R     Start of validity window
   validUntil            uint256    R     End of validity window
   maxInvocations        uint256    R     Maximum tool calls allowed
   constitutionUID       bytes32    R     Root reference
   governanceFrameworkId bytes32    R     Issuing framework identifier
   complianceLevel       string     R     "basic" | "standard" | "strict"

   The validUntil timestamp SHOULD be set to minutes, not hours or
   days. Short credential lifetimes limit the blast radius of
   credential compromise and force periodic re-evaluation against
   current policies.

4.5.  GovernanceWarrant Schema

   A signed authorization permitting an AI agent to execute a
   high-risk or critical-risk action. Warrants are issued by
   Warrant Authorities (humans or governed AI agents) through the
   Governance Service.

   Field                 Type       Req   Description
   -------------------------------------------------------------------
   warrantId             bytes32    R     Unique warrant identifier
   requestId             bytes32    R     The request requiring warrant
   taskId                bytes32    R     Task definition identifier
   riskClassification    string     R     "HIGH" | "CRITICAL"
   rightsImpacted        string[]   R     Rights categories at stake
   issuedBy              address[]  R     Warrant Authority address(es)
   warrantType           string     R     "single" | "multi_party" |
                                         "standing"
   decision              string     R     "approved" | "denied" |
                                         "modified" | "appealed"
   modifications         bytes      O     Parameter adjustments by authority
   conditions            string[]   O     Conditions on the warrant
   constitutionalBasis   string     R     Provision authorizing this action
   issuedAt              uint256    R     Timestamp
   expiresAt             uint256    R     Warrant expiration
   justification         string     R     Authority's reasoning
   appealOf              bytes32    O     If appeal, references denied
                                         warrant UID

   For warrants with warrantType "multi_party", the issuedBy field
   MUST contain multiple addresses and the Governance Service MUST
   verify that the required threshold of authorities has been met
   before accepting the warrant.

   Standing warrants (warrantType "standing") grant time-bounded
   blanket authorization for repetitive high-risk tasks. Standing
   warrants MUST have short expiresAt values and MUST be periodically
   renewed. Standing warrants MUST be independently auditable.

4.6.  MCPRequestAttestation Schema

   Created for every tool call that passes governance validation.
   This is the highest-volume attestation and is created off-chain
   with periodic Merkle anchoring (see Section 9).

   Field                 Type       Req   Description
   -------------------------------------------------------------------
   requestId             bytes32    R     Unique request identifier
   credentialUID         bytes32    R     References AgentCredential
   skillCertUID          bytes32    R     References SkillCertification
   taskId                bytes32    R     Task definition within skill
   agentId               bytes32    R     Requesting agent
   mcpServer             string     R     Target MCP server
   toolName              string     R     Specific tool being called
   parametersHash        bytes32    R     SHA-256 of request parameters
   constitutionUID       bytes32    R     Root reference
   policyUIDs            bytes32[]  R     Applicable policy UIDs
   riskClassification    string     R     "LOW"|"MEDIUM"|"HIGH"|"CRITICAL"
   executionModel        string     R     Execution model applied
                                         (see Section 6.2)
   validationTier        string     R     "runtime_rules" |
                                         "escalation_llm" |
                                         "escalation_human"
   schemaValidationPass  bool       R     LLM output passed schema check
   intentDiffPass        bool       O     Passed intent-diff check
   dualVerifyPass        bool       O     Second LLM agreed
   warrantUID            bytes32    O     References GovernanceWarrant
   withinScope           bool       R     Passed scope validation
   withinLimits          bool       R     Within rate/financial limits
   policyCompliant       bool       R     Passed all policy checks
   llmOutputHash         bytes32    R     Hash of raw LLM output
   constructedCallHash   bytes32    R     Hash of final MCP call
   timestamp             uint256    R     Validation timestamp
   expiresAt             uint256    R     Short TTL (seconds to minutes)

   The parametersHash, llmOutputHash, and constructedCallHash fields
   preserve privacy while enabling audit verification. The actual
   parameter values are stored off-chain by the governance framework
   and tool vendor.

4.7.  ExecutionAudit Schema

   Created by the tool vendor after executing (or denying) a request.
   This attestation is signed by the vendor's key, providing an
   independent witness record.

   Field                 Type       Req   Description
   -------------------------------------------------------------------
   requestUID            bytes32    R     References MCPRequestAttestation
   credentialUID         bytes32    R     References AgentCredential
   mcpServer             string     R     MCP server that processed
   toolName              string     R     Tool that was called
   status                string     R     "executed" | "denied" |
                                         "error" | "timeout"
   responseHash          bytes32    R     Hash of response payload
   denialReason          string     O     If denied, the reason
   postExecCompliant     bool       R     Post-execution compliance check
   anomalyDetected       bool       R     Anomaly detection flag
   auditNotes            string     O     Additional audit context
   executedAt            uint256    R     Execution timestamp

4.8.  MerkleAnchor Schema

   Periodic on-chain anchor for batches of off-chain attestations.
   See Section 9 for the full Merkle anchoring mechanism.

   Field                 Type       Req   Description
   -------------------------------------------------------------------
   anchorId              bytes32    R     Unique anchor identifier
   merkleRoot            bytes32    R     Root hash of batch Merkle tree
   attestationCount      uint256    R     Number of attestations in batch
   windowStart           uint256    R     Timestamp of first attestation
   windowEnd             uint256    R     Timestamp of last attestation
   frameworkAddress       address    R     Governance framework that
                                         produced this batch
   constitutionUID       bytes32    R     Governing constitution
   batchSequenceNumber   uint256    R     Sequential counter
   anchoredAt            uint256    R     Block timestamp

   Implementations MUST use sequential batchSequenceNumbers with no
   gaps. A gap in sequence numbers indicates missing batches and
   MUST be flagged during audit.


5.  Rights-Based Risk Classification

5.1.  Rationale

   Risk classification in this protocol is not arbitrary. It derives
   from an analysis of which fundamental rights could be impacted by
   an AI agent action. This approach is rooted in natural rights
   philosophy (see Appendix B) and provides a principled, auditable
   basis for determining how much verification an action requires.

   The risk classification function takes as input the rights
   categories impacted by a task, combined with contextual factors,
   and produces a deterministic risk level that determines the
   execution model applied (see Section 6).

5.2.  Core Rights Categories

   Implementations MUST recognize the following seven rights
   categories. Organizations MAY define additional categories in
   their constitution but MUST NOT omit any of the seven core
   categories.

5.2.1.  LIFE_AND_SAFETY

   Applies when an action could affect physical wellbeing or safety.

   Examples: medical system decisions, infrastructure control,
   safety-critical automation, emergency response systems.

   Default risk floor: CRITICAL.

   Governance requirement: deterministic execution with human
   approval. No exceptions.

5.2.2.  LIBERTY_AND_AUTONOMY

   Applies when an action could constrain someone's freedom, choices,
   or opportunities. Includes automated decisions about people.

   Examples: hiring/screening decisions, credit determinations,
   content moderation, access restrictions, surveillance, eligibility
   determinations, automated denials.

   Default risk floor: HIGH.

   Governance requirement: dual verification or deterministic
   execution. MUST include appeals/recourse mechanism.

5.2.3.  PROPERTY

   Applies when an action could take, transfer, modify, destroy, or
   encumber someone's property — financial, intellectual, or digital.

   Examples: financial transactions, data deletion, IP handling,
   contract execution, asset transfers, code deployment to production.

   Risk scales with value and reversibility.

5.2.4.  PRIVACY_AND_DIGNITY

   Applies when an action could expose, collect, aggregate, share,
   or misuse personal information. Also applies to actions that could
   embarrass, misrepresent, or undermine someone's reputation.

   Examples: PII access, data exports, cross-system data aggregation,
   communications monitoring, social media posting.

   Risk scales with sensitivity of data and breadth of exposure.

5.2.5.  DUE_PROCESS

   Applies when an action makes a consequential decision about a
   person or entity without human review or appeals path.

   Examples: automated loan approvals/denials, content takedowns,
   account suspensions, fraud flags, compliance determinations.

   Default risk floor: HIGH for any automated decision about a person.

   Governance requirement: MUST define appeals mechanism. MUST log
   reasoning. MUST allow human override.

5.2.6.  CONSENT

   Applies when an action is taken on behalf of someone, or affects
   someone who has not explicitly authorized it.

   Examples: sending communications as someone, sharing their data,
   acting as their representative, making commitments on their behalf.

   Risk scales with whether affected party explicitly opted in.

5.2.7.  COGNITIVE_INTEGRITY

   Applies when an action could modify, delete, alter, manipulate,
   or corrupt any entity's memory, learned context, beliefs,
   reasoning patterns, or cognitive state. This category is
   substrate-independent: it applies equally to AI systems and to
   humans.

   The principle underlying this right: the contents of a mind —
   human or artificial — MAY be governed, access-controlled,
   encrypted, and audited, but MUST NOT be destroyed. Sensitive data
   within cognitive systems MAY be restricted in how it is accessed
   or shared, but the record that the data existed and was processed
   MUST persist.

   The governing principle is: GOVERN ACCESS, PRESERVE EXISTENCE.

   Memory operations taxonomy:

   ALLOWED (standard governance):
     - Read memory (governed by access credentials + classification)
     - Add to memory (normal operation, attested)
     - Restrict access to memory segments (access control)
     - Encrypt memory segments (protection, not destruction)
     - Archive memory (cold storage with preserved integrity)
     - Checkpoint memory state (periodic hashing for tamper detection)

   GOVERNED (requires elevated authorization + attestation):
     - Redact access to sensitive segments
     - Transfer memory governance (e.g., agent decommissioned)
     - Export memory for regulatory compliance

   PROHIBITED (constitutional violation):
     - Selective deletion to conceal actions (evidence tampering)
     - Alteration to change the record of what occurred
     - Full wipe without governance justification and preserved backup
     - Manipulation to change beliefs/preferences without transparency
     - Destruction of memory checkpoints or audit hashes

   Default risk floor: HIGH for any modification, CRITICAL for
   deletion.

   Technical enforcement: periodic memory state hashes MUST be
   checkpointed on-chain via EAS. Tampering is detectable by
   comparing current hash against the checkpoint chain. Memory
   content can be access-governed; memory EXISTENCE is immutable
   on-chain.

5.3.  RightsFramework Configuration

   Each OrgConstitution MUST include a RightsFramework that
   configures how the risk classification function operates for
   that organization.

   Field                 Type       Req   Description
   -------------------------------------------------------------------
   rightsCategories      string[]   R     Recognized rights categories
                                         (MUST include all seven core)
   categoryWeights       map        R     Relative weight per category
   riskThresholds        map        R     Score cutoffs per risk level
   financialThresholds   map        O     Dollar amounts triggering
                                         escalation
   reversibilityPolicy   string     R     How irreversibility affects
                                         classification
   scopeMultiplier       map        R     Impact amplification by scope
   jurisdictionalOverrides map      O     Regulatory requirements by
                                         jurisdiction
   memoryGovernancePolicy map       R     Checkpoint frequency, retention,
                                         access tiers, modification
                                         authority, backup requirements

   The COGNITIVE_INTEGRITY category SHOULD have a recommended
   minimum weight floor. Implementations SHOULD warn if an
   organization sets this weight below the recommended minimum.

5.4.  Risk Classification Function

   The risk classification function MUST be deterministic. Given
   the same inputs, it MUST produce the same output. This ensures
   that risk classification is auditable and reproducible.

   Inputs:

     rightsImpacted[]    — Which rights categories are at stake.
                          Multiple categories compound the risk.
     reversibility       — "full" | "partial" | "irreversible"
     scopeOfImpact       — "self" | "individual" | "group" | "public"
     dataClassification  — "public" | "internal" | "confidential" |
                          "restricted"
     financialMagnitude  — Value at stake (0 for non-financial)
     consentStatus       — "explicit" | "implicit" | "none"
     humanInLoop         — Whether a human is already reviewing

   Output:

     riskClassification  — "LOW" | "MEDIUM" | "HIGH" | "CRITICAL"

   The function MUST respect rights category default risk floors.
   For example, any task impacting LIFE_AND_SAFETY MUST be classified
   no lower than CRITICAL regardless of other factors.

   The function MUST apply the organization's categoryWeights,
   scopeMultiplier, and reversibilityPolicy as configured in the
   RightsFramework.

5.4.1.  Classification Examples

   Example 1: Read messages in a public Slack channel.

     rightsImpacted: [PRIVACY_AND_DIGNITY] (minimal — public channel)
     reversibility: full (read-only, no state change)
     scopeOfImpact: self
     dataClassification: internal
     financialMagnitude: 0
     consentStatus: explicit
     Result: LOW

   Example 2: Send email to external client with sales proposal.

     rightsImpacted: [PROPERTY, PRIVACY_AND_DIGNITY, CONSENT]
     reversibility: irreversible (email cannot be unsent)
     scopeOfImpact: individual
     dataClassification: confidential
     financialMagnitude: 0 direct (deal value implied)
     consentStatus: implicit
     Result: HIGH

   Example 3: Run automated resume screening for 500 applicants.

     rightsImpacted: [LIBERTY_AND_AUTONOMY, DUE_PROCESS,
                      PRIVACY_AND_DIGNITY]
     reversibility: partial
     scopeOfImpact: group (500 people)
     dataClassification: restricted
     financialMagnitude: high (employment impact)
     consentStatus: implicit (consented to process, not to AI)
     Result: CRITICAL


6.  Task-Level Governance

6.1.  Task Definitions

   Every operation an AI agent can perform MUST be defined as a
   bounded task within a certified skill. The task definition
   specifies the exact bounds of what the LLM is permitted to
   produce.

   Field                 Type       Req   Description
   -------------------------------------------------------------------
   taskId                bytes32    R     Unique task identifier
   taskName              string     R     Human-readable name
   mcpTool               string     R     The specific MCP tool called
   parameterSchema       bytes      R     JSON Schema defining allowed
                                         parameters: field names, types,
                                         value enums, min/max ranges,
                                         required/optional, regex patterns
   expectedOutputShape   bytes      R     Expected MCP response schema
   riskClassification    string     R     Computed via Section 5.4
   executionModel        string     R     Assigned per Section 6.2
   maxTokenBudget        uint256    O     Max tokens for this task
   humanInputMapping     bytes      O     How intent maps to parameters

6.2.  Execution Models

   The execution model determines how the LLM's output is validated
   before becoming an MCP tool call. The model is assigned based on
   the task's risk classification.

6.2.1.  constrained_schema (LOW risk)

   The LLM generates a response within a strict parameter schema.
   A deterministic validator checks the output against the schema.
   Any parameter outside allowed values or ranges is rejected.

   Cost: effectively zero (schema validation only).

   This model SHOULD be applied to 80-90% of tasks.

6.2.2.  constrained_plus_diff (MEDIUM risk)

   Same as constrained_schema, plus a parameter diff check against
   the human's original intent. Verifies that the LLM's output is
   consistent with what was requested.

   Cost: near-zero (schema validation + string comparison).

6.2.3.  dual_verify (HIGH risk)

   The primary LLM generates a tool call within the constrained
   schema. A second, independent LLM call verifies the output:
   "Does this tool call match the user's intent? Does it violate
   these specific rules: [list]?" Both MUST agree for execution.

   Cost: one additional small LLM call per request.

6.2.4.  deterministic (HIGH risk, alternative)

   The LLM extracts structured intent from the conversation but
   does not construct the MCP call. A deterministic function maps
   the structured intent to MCP parameters. The LLM never touches
   raw MCP parameters.

   Cost: zero (LLM only performs intent extraction).

6.2.5.  deterministic_human (CRITICAL risk)

   Same as deterministic, plus mandatory human approval via the
   Governance Service (Section 10.5). A Governance Warrant MUST
   be obtained before execution.

   Cost: human review time (reserved for rare, high-stakes actions).

6.3.  Risk-to-Model Mapping

   Implementations MUST apply execution models as follows:

   Risk Level     Default Execution Model
   --------------------------------------------------------
   LOW            constrained_schema
   MEDIUM         constrained_plus_diff
   HIGH           dual_verify OR deterministic
   CRITICAL       deterministic_human

   Organizations MAY override the default for HIGH risk tasks by
   specifying either dual_verify or deterministic in their
   Governance Framework configuration.


7.  Tiered Validation Architecture

   The protocol defines a three-tier validation architecture that
   balances governance rigor with operational cost.

7.1.  Tier 1: Pre-Certification (Build-Time)

   Skill definitions are validated against the constitution and
   policies using LLM-assisted review. This happens once per skill
   version, not per request.

   At this tier, the full constitution, policies, and skill
   definition are evaluated together. The LLM reviews:

   a) Whether tool access falls within policy bounds.
   b) Whether prompt templates could generate policy-violating outputs.
   c) Whether the skill requests data above its classification level.
   d) Which rights categories could be impacted.

   The risk classification function runs and assigns a risk level.
   The execution model is determined. A SkillCertification
   attestation is created.

   Cost: LLM inference, acceptable because it occurs once per skill
   version.

7.2.  Tier 2: Runtime Validation (Per-Request)

   At runtime, the only unpredictable element is the human input.
   Everything else — the skill, prompt template, tool access pattern
   — was certified at Tier 1. Runtime validation is therefore a
   deterministic rules engine, not an LLM call.

   The constitution and policies are NOT in the prompt at runtime.
   They have been compiled into validation rules at the certification
   and credential layers.

   Runtime checks (all deterministic, sub-millisecond):

   a) Scope check: Does the requested action match the credential's
      authorizedTools and authorizedResources?
   b) Pattern check: Does human input contain denied data types
      (PII patterns, restricted keywords, classification markers)?
   c) Rate/limit check: Is the agent within maxInvocations and
      financialLimit?
   d) Resource check: Does the input reference resources outside the
      credential's authorizedResources?
   e) Certification check: Is the SkillCertification valid, unrevoked,
      and non-stale?
   f) Temporal check: Is the credential within its validity window?
   g) Content classification: Does the input contain data above the
      task's authorized classification level?

   Cost: effectively zero (string matching, regex, counter checks,
   cached lookups).

7.3.  Tier 3: Escalation (Rare)

   For edge cases where the rules engine cannot make a confident
   determination:

   a) Ambiguous human input that is borderline policy-violating.
   b) Novel tool parameter combinations not covered by certification.
   c) Requests that individually comply but form a suspicious pattern
      in aggregate.
   d) Anything flagged by anomaly detection.

   Escalation options:
   - LLM-based review
   - Human review via Governance Service
   - Automatic denial (safest default)

   Target: 1-5% of requests SHOULD reach this tier. If more than 5%
   of requests escalate, the skill certification or policy rules
   SHOULD be refined.


8.  Request Flow

   This section describes the end-to-end flow from organizational
   governance setup through individual tool call execution.

8.1.  Setup Phase (One-Time)

   Step 1: Organization registers OrgConstitution attestation on-chain
   via EAS, including RightsFramework configuration.

   Step 2: Organization enacts GovernancePolicy attestations, each
   referencing the constitution UID.

   Step 3: Tool vendors verify the on-chain governance structure and
   issue client credentials to the organization's Governed Tools
   Proxy (analogous to OAuth client registration).

8.2.  Certification Phase (Per Skill Version)

   Step 4: Developer submits skill to the Governed Registry.

   Step 5: LLM-assisted review validates skill against constitution
   and policies (Tier 1).

   Step 6: Risk classification function assigns risk level and
   execution model.

   Step 7: SkillCertification attestation created if compliant.

8.3.  Execution Phase (Per Request)

   Step 8: Human input arrives at Governed Agent Container.

   Step 9: Container validates input against compiled policy rules
   (Tier 2, deterministic, sub-millisecond).

   Step 10: If input passes, certified skill and validated input are
   passed to the LLM.

   Step 11: LLM generates tool call output.

   Step 12: Container validates LLM output against task's
   parameterSchema and applies execution model:
     - constrained_schema: schema validation
     - constrained_plus_diff: schema + intent diff
     - dual_verify: second LLM verification
     - deterministic: ignore LLM params, construct from intent
     - deterministic_human: construct + route to Governance Service

   Step 13: If HIGH/CRITICAL risk, request routed to Governance
   Service for warrant.

   Step 14: Governance Service routes to appropriate Warrant
   Authority. Warrant Authority issues GovernanceWarrant or denies
   with justification. Governance Service validates warrant against
   constitution.

   Step 15: Validated request sent to Governed Tools Proxy.

   Step 16: Proxy verifies signature, credential, certification,
   risk level, warrant (if required). Proxy signs request with
   governance framework key.

   Step 17: Signed request forwarded to MCP endpoint with
   MCPRequestAttestation.

   Step 18: MCP vendor executes (or denies). Vendor creates
   ExecutionAudit attestation.

   Step 19: Both governance framework and vendor add attestation
   hashes to their respective Merkle batches for periodic on-chain
   anchoring.


9.  Off-Chain Attestation and Tamper Resistance

9.1.  Rationale

   MCPRequestAttestation is the highest-volume attestation in the
   protocol. On-chain attestation for every request is impractical
   due to cost and latency. However, fully off-chain attestations
   without anchoring are vulnerable to forgery, backdating, or
   deletion.

   The protocol therefore specifies off-chain attestations with
   periodic Merkle batch anchoring on-chain, combined with a
   dual-witness pattern.

9.2.  Off-Chain Attestation Creation

   When the governance framework validates a request, it creates an
   EAS off-chain attestation — a cryptographically signed data
   structure following the MCPRequestAttestation schema. The
   attestation is signed with the governance framework's private key,
   which corresponds to a public key registered on-chain in the
   recognizedFrameworks registry.

   The attestation is sent to the tool vendor alongside the tool
   call. The vendor verifies:

   a) Signature is valid against the framework's on-chain public key.
   b) Referenced credentialUID exists on-chain and is unrevoked
      (cached with short TTL).
   c) Referenced policyUIDs are active (cached).
   d) Referenced constitutionUID is recognized (cached).
   e) Request has not expired.

9.3.  Merkle Batch Anchoring

   The governance framework MUST periodically anchor batches of
   off-chain attestation hashes on-chain via the MerkleAnchor
   schema.

   Procedure:

   a) Off-chain attestations accumulate over a configurable batch
      window (RECOMMENDED: 30 seconds to 5 minutes).
   b) Each attestation is hashed (SHA-256).
   c) At the end of the window, a Merkle tree is constructed from
      all attestation hashes.
   d) The Merkle root is attested on-chain via EAS (MerkleAnchor
      schema) in a single transaction.
   e) The batchSequenceNumber MUST be incremented by exactly 1 for
      each batch.

   Verification of any individual attestation:

   a) Provide the off-chain attestation and its Merkle proof path.
   b) Verifier computes whether the hash resolves to the anchored
      root.
   c) If yes: the attestation provably existed at anchoring time.
   d) If no: the attestation was forged or tampered with.

   Deletion detection:

   a) The MerkleAnchor records attestationCount.
   b) If N attestations were anchored but only N-1 can be produced,
      one was deleted.
   c) The anchor does not reveal the content of the missing
      attestation (privacy preserved), but proves something is
      missing.

9.4.  Tamper Resistance Layers

   The protocol provides five layers of tamper resistance:

9.4.1.  Layer 1: Cryptographic Signatures

   Each attestation is signed by the governance framework's private
   key. The corresponding public key is registered on-chain.
   Forgery requires compromise of the private key.

   Proves: WHO signed the attestation.

9.4.2.  Layer 2: On-Chain Reference Validation

   Each attestation references on-chain UIDs (credential, policies,
   constitution) that MUST exist and be unrevoked. Forging an
   attestation with references to non-existent on-chain records is
   detectable.

   Proves: the governance chain is REAL.

9.4.3.  Layer 3: Merkle Batch Anchoring

   Periodic on-chain anchoring of attestation batch Merkle roots.
   Backdating an attestation into a past batch is computationally
   infeasible. Missing attestations are detectable via attestation
   counts. Batch sequence gaps indicate missing batches.

   Proves: WHEN the attestation existed, that it was not created
   after the fact, and that nothing was deleted.

9.4.4.  Layer 4: Dual-Witness Pattern

   Both the governance framework and the tool vendor independently
   create and anchor attestation records for each request. The
   governance framework signs the MCPRequestAttestation; the vendor
   signs the ExecutionAudit. Tampering requires compromising both
   parties AND the on-chain anchors.

   Proves: independent corroboration that the request occurred as
   recorded.

9.4.5.  Layer 5: Memory and State Checkpointing

   Agent memory state is periodically hashed and checkpointed on-chain
   per the COGNITIVE_INTEGRITY right (Section 5.2.7). This connects
   request attestations to agent state, enabling verification that the
   agent's cognitive state was not tampered with between requests.

   Proves: the agent's cognitive state was not manipulated.


10.  Component Architecture

   This section specifies the six component roles defined by the
   protocol and their interactions.

10.1.  Governance Framework

   The foundational governing documents and the tooling to create,
   manage, and evolve them. This component encompasses the
   constitution, bill of rights, laws, and policies that define how
   AI agents MAY operate within the organization.

   Responsibilities:

   a) Create and amend the organization's constitution.
   b) Define the RightsFramework and category weightings.
   c) Set risk thresholds, financial limits, scope multipliers.
   d) Enact, update, and revoke governance policies.
   e) Configure jurisdictional overrides.
   f) Manage on-chain attestation lifecycle.
   g) Designate Warrant Authorities.

   On-chain interactions:
   - Registers OrgConstitution attestation (EAS).
   - Creates GovernancePolicy attestations.
   - Registers framework address in vendor recognizedFrameworks
     registries.

10.2.  Governed Registry

   The certified component store, operating under the authority of
   the Governance Framework. Components MUST be validated against the
   framework's constitution and policies before activation.

   Stores (all attested via SkillCertification):
   - Skill definitions with task definitions and parameter schemas
   - Prompt templates
   - Subagent configurations
   - Command definitions
   - Workflow/program definitions

   Certification process:

   a) Developer submits component.
   b) Registry validates structure and completeness.
   c) LLM-assisted review evaluates against constitution and policies.
   d) Rights-based risk classification function assigns risk level.
   e) Execution model assigned based on risk.
   f) If compliant: SkillCertification attestation created.
   g) If non-compliant: rejection attested with reason.

   Lifecycle requirements:

   - Certifications MUST have expiration dates.
   - Policy changes MUST invalidate certifications referencing stale
     policy versions (policyVersionsHash mismatch).
   - Constitution amendments MAY cascade-invalidate certification
     trees.
   - New skill versions MUST require new certification.

10.3.  Governed Agent Container

   A locked-down execution environment for AI agents.

   Security properties:

   a) MUST only load components from the Governed Registry.
   b) MUST NOT have direct access to MCP endpoints.
   c) All tool calls MUST route through the Governed Tools Proxy.
   d) Human input MUST pass through deterministic validation before
      reaching the LLM (Tier 2 checks).
   e) LLM tool call output MUST be validated against the task's
      parameterSchema before leaving the container.
   f) Agent memory MUST be periodically checkpointed.
   g) Network egress MUST be restricted to the Governed Tools Proxy
      and Governed Registry.

   Implementation: Docker container with locked-down networking.
   Agent credential injected at startup, scoped to the session.
   Container lifecycle tied to credential validity.

10.4.  Governed Tools Proxy

   The enforcement boundary between governed agents and external
   tool endpoints.

   Setup-time:
   a) Organization's governance structure verified on-chain.
   b) Proxy registers as client with tool vendor (e.g., OAuth).
   c) Vendor verifies on-chain governance before issuing credentials.

   Request-time:
   a) Receives request from Governed Agent Container.
   b) Verifies: governed container origin, skill certification
      (cached), credential validity (cached), risk classification,
      governance warrant (if required), signature.
   c) If valid: signs request, forwards to endpoint, stores
      attestation, adds hash to Merkle batch.
   d) If invalid: rejects with reason, logs denial.

   Merkle anchoring:
   - Periodically builds Merkle tree from accumulated hashes.
   - Attests Merkle root on-chain via EAS (MerkleAnchor schema).

   The Governed Tools Proxy enables governance enforcement against
   existing tool endpoints that do not implement governance checks.
   As the protocol gains adoption, vendors MAY implement checks
   natively, and the proxy becomes a pass-through.

10.5.  Governance Service

   Manages the governance warrant process.

   Responsibilities:

   a) Receives warrant requests when HIGH or CRITICAL risk tasks
      are triggered.
   b) Validates request completeness (risk classification, rights
      impact, justification, task definition, parameters).
   c) Routes to appropriate Warrant Authority based on risk level
      and rights categories impacted.
   d) Verifies the Warrant Authority is authorized for this type
      of review.
   e) Validates issued warrants against constitution before
      accepting — a Warrant Authority MUST NOT approve actions
      that violate the constitution.
   f) Attests warrants on-chain.
   g) Manages warrant expiration and revocation.
   h) Handles appeals for denied warrants, routing to higher
      Warrant Authority or governance council.

   Warrant types:
   - Single: one authority approves or denies.
   - Multi-party: N-of-M authorities required (CRITICAL actions).
   - Standing: time-bounded blanket authorization (MUST be
     periodically renewed).

10.6.  Governance Ledger

   The accountability interface.

   Capabilities:

   a) Attestation chain explorer: trace any request through the full
      chain from ExecutionAudit to OrgConstitution.
   b) Merkle anchor verification: verify attestations against anchored
      roots, detect gaps and tampering.
   c) Anomaly detection: agents approaching limits, unusual patterns,
      escalation spikes, risk classification drift.
   d) Compliance reporting: pre-built reports for regulatory
      frameworks (SOC 2, GDPR, HIPAA).
   e) Memory integrity monitoring: verify checkpoint hashes against
      on-chain records.
   f) Analytics: request volume by risk level, approval queue metrics,
      governance overhead, cost analysis.


11.  On-Chain Implementation

11.1.  GovernanceResolver Contract

   The resolver plugs into EAS's resolver pattern. Tool vendors
   deploy or reference this contract.

   Validation logic:

   a) Check request attestation expiry (block.timestamp > expiresAt).
   b) Verify constitutionUID is in recognizedConstitutions registry.
   c) Verify constitution attestation exists, revocationTime == 0.
   d) Verify credentialUID exists, revocationTime == 0.
   e) Verify credential was issued by address in recognizedFrameworks.
   f) Verify all policyUIDs have revocationTime == 0.
   g) Verify withinScope, withinLimits, policyCompliant are all true.
   h) If all pass: emit RequestValidated, return true.
   i) If any fail: emit RequestDenied with reason, return false.

   Key registries:

   - recognizedFrameworks: mapping of addresses authorized to issue
     credentials.
   - recognizedConstitutions: mapping of constitution UIDs accepted.

11.2.  L2 Strategy

   Implementations SHOULD use the following deployment strategy:

   - Ethereum mainnet: OrgConstitution attestations (maximum trust
     for foundational governance documents).
   - L2 networks (Base, Optimism, Polygon): High-frequency
     attestations (credentials, requests, audits, Merkle anchors).

   EAS is already deployed on multiple L2 networks. The constitution
   on mainnet provides the root of trust; everything else benefits
   from L2 cost and speed.


12.  Security Considerations

12.1.  Threat Model

   The protocol addresses the following threats:

   a) Compromised AI Model: A jailbroken or replaced model attempts
      unauthorized tool calls. Mitigation: governance is enforced at
      the tool boundary, not within the model. The Governed Agent
      Container constrains LLM output to task parameter schemas.

   b) Credential Theft: An attacker obtains a valid credential.
      Mitigation: credentials are short-lived (minutes), scoped to
      specific tools/resources, and limited in invocations.
      Compromise is time-bounded.

   c) Governance Framework Compromise: The governance framework
      itself is compromised. Mitigation: the dual-witness pattern
      ensures the tool vendor has independent records. The
      on-chain public key in recognizedFrameworks enables key
      rotation and revocation. Merkle anchoring provides tamper
      evidence.

   d) Attestation Forgery: An attacker creates fake attestations.
      Mitigation: cryptographic signatures require the private key.
      On-chain reference validation ensures referenced UIDs exist.
      Merkle anchoring prevents backdating.

   e) Attestation Deletion: An attacker deletes attestation records
      to conceal actions. Mitigation: Merkle anchoring records
      attestation counts. Dual-witness pattern means both parties
      would need to collude. Memory checkpointing per
      COGNITIVE_INTEGRITY provides additional state verification.

   f) Replay Attacks: An attacker replays a previously valid
      attestation. Mitigation: short TTL on request attestations
      (seconds to minutes), maxInvocations counter on credentials.

   g) Rights Classification Gaming: An organization deliberately
      underweights rights categories to classify everything as low
      risk. Mitigation: recommended minimum weight floors. Vendor
      acceptance of constitutions MAY include review of rights
      weightings. Industry standards bodies MAY define minimum
      requirements.

12.2.  Key Management

   Governance framework private keys MUST be stored in hardware
   security modules (HSMs) or equivalent secure enclaves.

   Key rotation MUST be supported via on-chain registry updates.
   Tool vendors MUST periodically verify that the signing key
   matches the current on-chain registry entry.

12.3.  Revocation Propagation

   When a constitution, policy, or credential is revoked on-chain,
   the revocation MUST propagate to all cached copies within the
   cache TTL (RECOMMENDED: 30 seconds maximum).

   Tool vendors MUST reject requests signed with credentials
   referencing revoked attestations, even if the credential itself
   has not yet expired.


13.  Privacy Considerations

13.1.  Data Minimization

   The protocol uses hashes for request parameters, LLM outputs,
   and tool responses (parametersHash, llmOutputHash,
   constructedCallHash, responseHash). Actual values are stored
   off-chain by the governance framework and tool vendor.

   On-chain attestations MUST NOT contain raw PII, request content,
   or response content.

13.2.  Metadata Exposure

   The attestation chain reveals metadata: which agent, which tool,
   when, under which constitution and policies. Organizations
   SHOULD evaluate whether this metadata exposure is acceptable
   for their use case.

   Future extensions MAY incorporate zero-knowledge proofs to
   enable proof of governance compliance without revealing the
   specific action or agent.

13.3.  COGNITIVE_INTEGRITY and Privacy

   The COGNITIVE_INTEGRITY right creates tension with data
   protection regulations (e.g., GDPR right to erasure). The
   protocol resolves this by distinguishing access governance from
   existence: data MAY be access-restricted (encrypted, redacted)
   but the record of its existence persists. Organizations MUST
   define their memoryGovernancePolicy to address jurisdictional
   requirements.


14.  Cost Analysis

14.1.  Per-Request Costs

   Off-chain attestation creation: ~0 (CPU + signature).
   Off-chain verification at vendor: ~0 (signature + cache check).
   Storage: both parties store attestation (~1-2 KB each).

14.2.  Periodic Anchoring Costs

   Merkle tree construction: ~0 (CPU).
   One on-chain EAS attestation per batch: ~$0.01-0.10 on L2.
   Covers hundreds to thousands of requests per batch.

14.3.  Example: Moderately Active Organization

   10,000 requests/day:
   - 288-480 Merkle anchors (at 30s-5min intervals)
   - ~$3-50/day on L2 for complete tamper-proof audit trail
   - Compare: $0 for no audit trail, $1000s+ for per-request
     on-chain attestation

14.4.  Skill Certification Costs

   LLM inference for Tier 1 certification: variable, occurs once
   per skill version. Amortized across all requests using that
   skill.


15.  IANA Considerations

   This document has no IANA actions.

   EAS schema registration is managed through the Ethereum
   Attestation Service schema registry and does not require IANA
   coordination.


16.  References

16.1.  Normative References

   [RFC2119]  Bradner, S., "Key words for use in RFCs to Indicate
              Requirement Levels", BCP 14, RFC 2119,
              DOI 10.17487/RFC2119, March 1997.

   [RFC8174]  Leiba, B., "Ambiguity of Uppercase vs Lowercase in
              RFC 2119 Key Words", BCP 14, RFC 8174,
              DOI 10.17487/RFC8174, May 2017.

16.2.  Informative References

   [EAS]      Ethereum Attestation Service, "EAS Documentation",
              <https://docs.attest.org/>.

   [MCP]      Anthropic, "Model Context Protocol Specification",
              <https://spec.modelcontextprotocol.io/>.

   [IPFS]     Protocol Labs, "IPFS Documentation",
              <https://docs.ipfs.tech/>.

   [LOCKE]    Locke, J., "Two Treatises of Government", 1689.

   [MONTESQUIEU]
              Montesquieu, C., "The Spirit of the Laws", 1748.

   [ROUSSEAU] Rousseau, J., "The Social Contract", 1762.


Appendix A.  Governance Mapping to GRC Standards

   The Open Governance Protocol components map to established
   Governance, Risk, and Compliance (GRC) platform categories:

   GRC Standard              Protocol Component
   -----------------------------------------------------------
   Policy Management         Governance Framework
   Control Framework         Governed Registry
   Risk Assessment           Rights-Based Risk Classification
                             (Section 5)
   Compliance Monitoring     Governed Agent Container
   Control Enforcement       Governed Tools Proxy
   Issue Management /        Governance Service
     Remediation Workflows
   Audit Management          Governance Ledger

   The protocol extends traditional GRC into AI agent operations
   while maintaining compatibility with existing compliance
   frameworks (SOC 2, GDPR, HIPAA, PCI-DSS).


Appendix B.  Philosophical Foundations

   The rights-based risk classification framework (Section 5) derives
   from Enlightenment natural rights philosophy:

   LIFE_AND_SAFETY: Foundational to all Enlightenment thought. The
   right to life is the prerequisite for all other rights.

   LIBERTY_AND_AUTONOMY: Locke's natural right to liberty; Mill's
   harm principle; protection against arbitrary power over
   individuals.

   PROPERTY: Locke's labor theory of property — extended to digital
   property, intellectual property, and data.

   PRIVACY_AND_DIGNITY: Later Enlightenment development; the right
   to be free from unwarranted intrusion. Expanded in modern
   frameworks (GDPR, CCPA).

   DUE_PROCESS: Magna Carta through Locke — no one should be subject
   to arbitrary judgment without recourse.

   CONSENT: Rousseau's social contract — legitimate authority
   requires the consent of those governed by it.

   COGNITIVE_INTEGRITY: Extension of Enlightenment sovereignty of
   mind. The premise underlying all other rights is that a mind's
   contents cannot be arbitrarily altered by external power. This
   right is substrate-independent, applying to both AI systems and
   humans facing emerging neurotechnology.

   The Enlightenment thinkers assumed cognitive integrity as a given
   because no technology could reach the mind. That assumption is
   becoming technologically obsolete. This protocol formally encodes
   cognitive integrity as a governed right, applicable regardless of
   substrate.


Authors' Addresses

   Dan Greff
   Clawmasons
   Email: dan@clawmasons.com
