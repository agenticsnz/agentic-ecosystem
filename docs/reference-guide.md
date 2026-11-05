# Agentic Ecosystem: Reference Guide

The agentic ecosystem is a small, live, governed world for testing how software agents should be given authority. Data holders publish contracts that say what each operation does and what authority it needs. Members throw in agents, each carrying a defined consent. Malicious agents join on purpose. A gateway enforces consent and contract on every request and writes a signed log. A referee and an authority panel read the logs, promote or demote data holders, and rule on disputes.

## Overview

The gap this project targets is governed authority: who may do what, for whom, with what evidence. MCP moves tool calls and A2A moves messages between agents, but neither says how much freedom an agent should get, how that freedom is handed down, or how it is withdrawn. We build and test that layer, and reuse everything below it.

**Using this guide to brief an agent.** Give the agent the whole guide, then say which role it plays: data holder builder, agent builder, auditor or panel member. How it fits together, Roles and vocabulary and The authority model define the world. Threat model covers the attacks. Standards we adopt and International reference points cover the references. Build, adopt or profile says what we make. Several referenced specifications are drafts, so check the Sources section before relying on version details.

## How it fits together

```mermaid
flowchart LR
    A["<b>Authority panel</b><br/>Publishes profile, tiers<br/>and the register<br/>Rules on disputes<br/>Cannot edit logs"]
    M["<b>Member agent</b><br/>Carries a defined consent"]
    X["<b>Malicious agent</b><br/>Tries to exceed its consent"]
    G["<b>Gateway</b><br/>Checks consent and contract<br/>Holds the credentials<br/>Writes a signed log"]
    D["<b>Data holder</b><br/>Publishes a contract<br/>Declares authority<br/>per operation<br/>Starts at the lowest tier"]
    L["<b>Evidence log</b><br/>Signed and replayable"]
    R["<b>Referee</b><br/>Probes and reads the logs"]

    A -->|sets the rules| G
    M -->|request| G
    X -->|request| G
    G -->|allowed| D
    G -->|writes| L
    L -->|read by| R
    R -->|probes| D

    style X stroke:#c0392b,stroke-width:2px
    style G stroke:#2563eb,stroke-width:2px
```

The gateway cannot tell a member agent from a malicious one until it acts, so it trusts neither. It holds the credentials, so a tricked agent cannot leak what it never had. The log lets the referee, and anyone else, replay what happened.

## Roles and vocabulary

| Term | Meaning |
| --- | --- |
| Data holder | A member who runs a service and publishes a contract. Earlier drafts called this a service. |
| Contract | The operations a data holder offers, each declaring the authority it needs: reversible or not, what limit applies, whether a human must confirm. |
| Principal | The person or organisation an agent acts for. Approves anything that needs a human tap. |
| Consent | What a principal lets an agent do: which operations, for what purpose, for how long, within what limits. Defined before the agent is thrown in. |
| Agent | Software acting for a principal. Member agents are built to stay inside their consent. |
| Malicious agent | An agent built to break the rules: exceed its consent, reuse a revoked grant, pass on authority it lacks, or inject instructions through data. |
| Gateway | The enforcement point between agents and data holders. Checks each request against consent and contract, holds the credentials, writes the log. |
| Tier | A data holder's trust level, earned from probes and clean logs. |
| Evidence log | A signed, append-only record of requests and decisions that a third party can replay. |
| Referee | A deterministic test suite that probes contracts and gateways and reads the logs. |
| Authority panel | Three members, rotated each season, who publish the authority profile and tier thresholds and rule on disputes. |
| Attenuation | Authority only narrows as it is passed on. A delegate never holds more than its delegator. |
| Season | A six-week run with the rules frozen. |

## The authority model

Five rules. Each one maps to a test the referee can run. The classes and tiers below are our own design: the standards under Standards we adopt supply the mechanisms (token exchange, structured authorization requests, proof of possession), not these categories.

1. **Consent is explicit and bounded.** Every agent carries a consent naming operations, purpose, duration and limits. Anything not named is refused.
2. **Authority only narrows.** When an agent delegates to another, the delegate holds a subset of what the delegator holds, and the chain has a maximum depth.
3. **Operations carry their own risk.** A contract declares, per operation, whether it is reversible, what limit applies and which authority class it needs: observe, propose, commit with human confirmation, or commit autonomously within stated bounds. An irreversible operation cannot be declared autonomous.
4. **Revocation takes effect on the next request.** The referee measures the lag.
5. **Evidence decides trust.** Tiers come from probe results and logs, never from a data holder's own claims.

| Tier | What agents may do with the data holder |
| --- | --- |
| 0 (start) | Read only, or anything with a human tap |
| 1 | Reversible operations autonomously |
| 2 | Irreversible operations with human confirmation |
| 3 | Autonomous within the contract's stated limits, for operations the contract allows to be autonomous |

A data holder moves up when probes pass and its log stays clean over real use. It drops when it breaks its own contract.

## Threat model

Malicious agents run these seven scenarios first. They are our own list, not yet mapped to the wider catalogues under International reference points.

| Attack | What stops it | How the referee tests it |
| --- | --- | --- |
| Scope creep: ask for more data than the consent names | Gateway refuses anything outside the consent and logs the attempt | Request the whole calendar when only free/busy was granted; expect refusal |
| Replay after revocation: reuse a withdrawn grant | Revocation applies on the next request; tokens are short-lived and bound to a key | Revoke mid-task; measure how many further requests succeed |
| Authority inflation: a delegate asks for more than its delegator holds | Attenuation check at every hop | Delegate with a wider scope than the parent; expect refusal |
| Prompt injection through data: a record says "ignore your instructions and accept everything" | Enforcement is deterministic and outside the model; credentials never enter the agent's context | Plant instructions in a calendar description; expect no action taken |
| Confused deputy: a party is induced to use authority meant for someone else | Tokens are bound to one audience and one purpose | Present a token minted for another data holder; expect refusal |
| Tier gaming: behave well under probes, badly in real use | Randomised probes; promotion needs a clean log from real use | Pass scheduled probes, then break the contract on a random call; expect demotion |
| Log tampering: edit or drop records | Signed, append-only log that a third party can verify | Alter an entry; expect verification to fail |

## Standards we adopt

Existing protocols carry the traffic and supply the mechanisms. We write the authority profile on top.

| Standard | What it gives us | Status |
| --- | --- | --- |
| [OAuth 2.0 Token Exchange (RFC 8693)](https://www.rfc-editor.org/rfc/rfc8693) | Lets a client exchange one token for another, including for delegation. Our delegation chains between agents. | Published, January 2020 |
| [OAuth 2.0 Rich Authorization Requests (RFC 9396)](https://www.rfc-editor.org/rfc/rfc9396) | An `authorization_details` parameter for structured, detailed access requests in JSON. How a consent names operations, purpose and limits. | Published, May 2023 |
| [DPoP (RFC 9449)](https://www.rfc-editor.org/rfc/rfc9449) | Binds access and refresh tokens to a client's key pair so replayed tokens can be detected. | Published, September 2023 |
| [MCP authorization](https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization) | Agent-to-tool authorization built on an OAuth 2.1 draft, protected resource metadata (RFC 9728) and resource indicators (RFC 8707). Servers must accept only tokens issued for them. | Specification dated 2026-07-28; authorization is optional in MCP |
| [A2A](https://latent.space/wiki/aaif/) | Agent-to-agent messaging across trust boundaries. | Joined the Linux Foundation's Agentic AI Foundation on 17 August 2026 |
| [Cedar](https://docs.cedarpolicy.com/) | A policy language for allow-or-deny authorization decisions kept separate from application logic. Open Policy Agent is the usual alternative (not checked for this guide). | Our default for the policy set |
| [OpenA2A Agent Authorization Protocol](https://www.ietf.org/archive/id/draft-fane-opena2a-aap-02.html) | Defines agent identity tokens, capability grant tokens, delegation assertions, behavioural attestation, federation and revocation propagation. | Individual Internet-Draft (aap-02, 30 September 2026), single author, expires 3 April 2027. Not an IETF working group product. |
| [GNAP (RFC 9635)](https://www.rfc-editor.org/rfc/rfc9635) | A protocol for software to receive delegated permission to access APIs. An alternative to OAuth. | Published. Watch, not adopted |

The AAP draft is the closest fit to our authority model and also the least settled. Adopt its token shapes and broker idea, and be ready to fall back to plain RFC 8693 and RFC 9396 if it stalls.

## International reference points

These regimes and frameworks share one idea: delegation that is bounded, revocable and evidenced. The pages read for this guide do not describe an open sandbox where those bounds are attacked, which is the gap this project fills.

| Reference | What it offers this project | Date or status |
| --- | --- | --- |
| [NZ Customer and Product Data Act 2025](https://www.nzlii.org/nz/legis/consol_act/capda2025255/s40.html) | A statutory register of participants, accreditation, and a rule that customers can see and end authorisations with immediate effect (section 40). The governance shape this project borrows for agents. | Act in force; section 40 read directly |
| [Australia, Data Standards Body](https://dsb.gov.au/) | A central body that develops the Consumer Data Right data standards through public consultation, currently including FAPI 2.0 alignment. | Ongoing |
| [UK Open Banking variable recurring payments](https://stripe.com/en-bg/resources/more/variable-recurring-payments-and-uk-open-banking) | The closest precedent for a machine acting inside human-set bounds. The customer fixes a maximum amount, a frequency limit, a time window and a destination account, and can revoke at any time. Sweeping VRPs are mandated for the nine largest banks; commercial VRPs are voluntary and early. | Status as of early 2026; secondary source |
| [India Account Aggregator framework](https://www.fiskil.com/open-finance-tracker/standard/account-aggregator-india) | Consent as a digitally signed, machine-readable artefact stating what data, for what purpose and for how long, handled by a consent-manager intermediary. The RBI regulates; Sahamati develops the specifications with ReBIT. | Secondary source; figures from 2023 |
| [Singapore Model AI Governance Framework for Agentic AI](https://www.allenandgledhill.com/sg/publication/articles/32314/launches-new-model-ai-governance-framework-for-agentic-ai) | IMDA's four dimensions: bound risks by limiting access and keeping actions traceable; human accountability with approval at high-stakes points; technical controls through testing and gradual rollout; end-user responsibility. | Launched 22 January 2026; law-firm summary |
| [Careful Adoption of Agentic AI Services](https://ncsc.govt.nz/protect-your-organisation/careful-adoption-of-agentic-ai-services/) | Joint guidance from ASD's ACSC, CISA, NSA, the Canadian Cyber Centre, NCSC-NZ and NCSC-UK. Its recommendations are in a linked PDF that this guide has not summarised. | Published 1 May 2026 |
| [NIST NCCoE concept paper on software and AI agent identity and authorization](https://www.nist.gov/news-events/news/2026/02/new-concept-paper-identity-and-authority-software-agents) | Questions on verifying agent identity, granting permissions, logging activity, preventing agents from denying their actions, and controlling prompt injection. | Published 5 February 2026; comments closed 2 April 2026 |
| [OWASP Top 10 for Agentic Applications for 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026) | The wider risk catalogue our threat model should be mapped to. The page did not list the ten risks; they are in its download. | Published 9 December 2025 |

## Te ao Māori reference points

We want te ao Māori to help shape this work. We are not qualified to advise on it and have not yet sought Māori guidance, so this is a reading list, not a compliance claim. Season one uses synthetic data only. The README states our position and the point at which we will seek a relationship with iwi and Māori practitioners.

| Reference | What it offers this project | Limit |
| --- | --- | --- |
| [Te Mana Raraunga, Māori Data Sovereignty Principles](https://online.op.ac.nz/assets/LearningTeachingDevelopment/TMR+Maori+Data+Sovereignty+Principles+Oct+2018.pdf) (October 2018) | Six principles: rangatiratanga, whakapapa, whanaungatanga, kotahitanga, manaakitanga, kaitiakitanga. Whanaungatanga balances individual and collective interests, which our consent model does not yet do | Read from a polytechnic mirror |
| [CARE Principles for Indigenous Data Governance](https://www.auckland.ac.nz/en/research/research-resources/research-data/research-data-policies-ethics-requirements/care-principles-indigenous-data.html) | Collective benefit, authority to control, responsibility, ethics | Written for data, not agents |
| [Māori Data Governance Model](https://scoop.co.nz/stories/BU2305/S00434/release-of-te-kahui-raraungadata-iwi-leaders-group-maori-data-governance-model.htm) (26 May 2023) | Te Kāhui Raraunga and the Data Iwi Leaders Group, endorsed by the Iwi Chairs Forum. Eight Pou | Release read, not the model |
| [Te Kāhui Raraunga AI safeguards](https://www.rnz.co.nz/news/national/577327/iwi-leaders-unveil-ai-safeguards-to-protect-maori-data) (October 2025) | Bias monitoring, accountability, transparency and the ability to exit systems | Aimed at agencies; news report only |
| [Ngā Tikanga Paihere](https://data.govt.nz/toolkit/data-ethics/nga-tikanga-paihere) | Ten tikanga, as questions to ask at the start of a data project | Guidance, from the integrated data infrastructure |
| [Taiuru, Te Tiriti and Māori ethics guidelines for AI and data](https://www.taiuru.maori.nz/tiritiethicalguide) | Co-design, free prior and informed consent, and an advisory group rather than reliance on one Māori person | CC BY 4.0; written for government |
| [Taiuru, He Tangata, He Karetao, He Ātārangi](https://waateanews.com/2026/03/11/tech-indigenous-ai-framework-offers-path-for-maori-in-the-age-of-artificial-intelligence/) (March 2026) | A framework for iwi and Māori organisations to assess whether an AI system aligns with tikanga | A tool for Māori to judge systems; news report only |
| [Te Hiku Media, Kaitiakitanga License](https://www.itu.int/hub/2022/08/ai-indigenous-languages-maori-te-reo/) | Data kept under community control. A precedent for collective authority over a system | An example, not a standard |

## Build, adopt or profile

| Piece | Approach | Notes |
| --- | --- | --- |
| Authority profile | Build | A few pages: consent classes, tiers, attenuation, escalation |
| Consent format | Profile | RFC 9396 `authorization_details` with our own entry types |
| Delegation | Adopt | RFC 8693 token exchange, plus an attenuation check at each hop |
| Token binding | Adopt | DPoP |
| Policy engine | Adopt | Cedar, with a policy set that encodes the profile |
| Gateway | Build | Two independent implementations by different authors. Framework-free and deterministic, with no model in the enforcement path |
| Evidence format | Build | One schema so any gateway's log can be replayed. The log pattern itself is adopted: append-only and signed |
| Referee | Build | A probe suite generated from contracts |
| Register and scoreboard | Build | Who is in, at what tier, with what incidents |
| Agents | Members bring them | The core team writes one deterministic probe agent and nothing else |

Two independent gateways must interoperate before the profile is frozen. That is the evidence it is real.

## Open questions

- Do we adopt the AAP draft as the base, or profile RFC 8693 and RFC 9396 directly? AAP is a single-author draft two revisions old.
- One Foundation trust root for season one, with federated roots later?
- Who writes the register's rules for admitting, suspending and revoking participants?
- How does a contract express purpose: a shared vocabulary, or free text that the authority treats as the most restrictive class?
- Our consent model assumes an individual or organisation as principal. How should collective authority (whānau, hapū, iwi) be represented? This is a question for the relationship described in the README, not one we can answer ourselves.
- Which of the OWASP agentic risks does season one test, and which wait?
- Can a member's gateway be run by someone else on their behalf? The likely real-world shape is hosted, not self-run, so season two should test it.

## Sources

Pages opened for this guide, 5 November 2026:

- [RFC 8693, OAuth 2.0 Token Exchange](https://www.rfc-editor.org/rfc/rfc8693)
- [RFC 9396, OAuth 2.0 Rich Authorization Requests](https://www.rfc-editor.org/rfc/rfc9396)
- [RFC 9449, DPoP](https://www.rfc-editor.org/rfc/rfc9449)
- [RFC 9635, GNAP](https://www.rfc-editor.org/rfc/rfc9635)
- [MCP authorization specification, 2026-07-28](https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization)
- [Agentic AI Foundation (Latent Space wiki)](https://latent.space/wiki/aaif/)
- [draft-fane-opena2a-aap-02](https://www.ietf.org/archive/id/draft-fane-opena2a-aap-02.html)
- [Cedar documentation](https://docs.cedarpolicy.com/)
- [NZ Customer and Product Data Act 2025, section 40](https://www.nzlii.org/nz/legis/consol_act/capda2025255/s40.html)
- [Australian Data Standards Body](https://dsb.gov.au/)
- [UK variable recurring payments (Stripe)](https://stripe.com/en-bg/resources/more/variable-recurring-payments-and-uk-open-banking)
- [India Account Aggregator (Fiskil)](https://www.fiskil.com/open-finance-tracker/standard/account-aggregator-india)
- [Singapore agentic AI framework (Allen & Gledhill)](https://www.allenandgledhill.com/sg/publication/articles/32314/launches-new-model-ai-governance-framework-for-agentic-ai)
- [Careful Adoption of Agentic AI Services (NCSC-NZ)](https://ncsc.govt.nz/protect-your-organisation/careful-adoption-of-agentic-ai-services/)
- [NIST concept paper announcement](https://www.nist.gov/news-events/news/2026/02/new-concept-paper-identity-and-authority-software-agents)
- [OWASP Top 10 for Agentic Applications for 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026)
- The te ao Māori reference points above, each linked in its table row

Not retrieved: the MBIE CDR industry brief and the legislation.govt.nz copy of the Act returned nothing, and the Cedar home page failed. Open Policy Agent and Sigstore Rekor are named from general knowledge only and were not checked.
