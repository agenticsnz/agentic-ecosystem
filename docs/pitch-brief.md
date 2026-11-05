# Agentic Ecosystem: Pitch Brief

We are building a live, open agentic ecosystem where members throw agents into a governed marketplace, alongside deliberately malicious ones, and watch how the rules hold up.

## The idea

Agents will soon act for people across organisations: booking, buying, sharing data. The rules for what an agent may do, on whose say-so, and with what evidence, have not been tested outside slideware. We build a small live world to test them.

Services publish contracts. Members throw agents in, each with a defined consent. Some agents are built to misbehave. We watch what holds and what breaks, and we write it up.

## How it works

1. **Services publish contracts.** A service offers operations (hold a room, share a calendar, publish a newsletter) and declares the authority each one needs: reversible or not, what limit applies, whether a human must confirm.
2. **Members throw in agents, each with a defined consent.** The consent says what the agent may do, for whom, and for how long. Nothing more.
3. **Malicious agents join on purpose.** They try to exceed their consent, reuse revoked grants, pass on authority they don't hold, and smuggle instructions through data.
4. **Everything is recorded and replayable.** A gateway sits between each agent and each service, checks every request against the consent and the contract, and writes a signed log anyone can verify.
5. **Trust is earned.** A new service starts at the lowest tier, where every action needs a human tap. Passing probes and keeping a clean log promotes it. Breaking its own contract demotes it.

A model never sits in the enforcement path. Decisions are deterministic.

## Why it matters

- **For members:** your agent represents you to other people's agents. You get a first win in an hour (watch your agent receive its first refusal), monthly break-it nights and a public scoreboard.
- **For the community:** the first tested evidence, from a New Zealand group, of how agent authority holds and fails. Published openly, free to fork.
- **For standards work:** every hole found is a finding for whoever writes the rules later. Failing in a sandbox is cheap.

This is an independent collective project. It does not speak for any agency or employer.

## What we build and what we adopt

We write as little new standard as possible. Existing protocols carry the traffic; we build the layer that governs it.

| Layer | Adopt as is | Build |
| --- | --- | --- |
| Transport | A2A between agents, MCP between agents and tools | Nothing |
| Authority | OAuth token exchange, rich authorization requests, proof-of-possession tokens | A short authority profile: consent classes, attenuation rules, escalation |
| Enforcement | A policy engine such as Cedar or OPA | The gateway and its policy set |
| Evidence | A transparency log such as Sigstore Rekor | A shared evidence format so any node's log can be replayed |
| Governance | Nothing | Register, test kit, tier calculator, public scoreboard |

## Season one

Six weeks with the rules fixed: three starter services (venue, speaker slots, newsletter), five or six agents, a panel of three, a break-it night in week five and one scripted incident in week six. Stakes stay low: calendars, bookings, votes and drafts. No money, no private data. The full run is in [How a Season Works](how-a-season-works.md).

## What we need to start

- [ ] Three or four core builders for the engine: gateway, test kit, evidence log
- [ ] Three authority panel members who are not also running services
- [ ] Three service builders to write the first contracts
- [ ] Agent builders, in any framework
- [ ] Auditors and attackers for break-it nights

**The ask:** pick a role and put your name against it. If we can't name the core builders, we don't launch.
