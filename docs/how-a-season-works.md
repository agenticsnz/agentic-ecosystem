# How a Season Works

A season is six weeks of a running agent network where the rules stay fixed, members build and play inside them, and every failure is written up as input to the next season's rulebook.

## Who plays

Everyone takes one role per season and can switch the next. Nobody plays two roles that rule on each other's work.

| Role | What you do | Who |
| --- | --- | --- |
| Authority panel | Runs the test kit, publishes tier thresholds, rules on disputes | Three members, rotated each season |
| Data holder | Builds and runs a data holder (venue, speakers, newsletter) and publishes its contract: the short risk note for each operation | Any member |
| Agent builder | Builds an agent that uses data holders within the consent its principal gave it | Any member |
| Principal | The human an agent works for; taps approve when an agent needs a yes | Every member with an agent |
| Auditor or attacker | Throws in malicious agents or hunts for rule-breaking, and writes up what they find | Any member |

A small core team of three or four builds and maintains the engine: the test kit, the trust calculator and the log format. Core members play in the world but never sit on the authority panel.

## Six weeks

1. **Week 1, setup.** Roles picked, the authority panel named, three data holders started at the lowest trust tier. Every action needs a human tap.
2. **Week 2, first probes.** The test kit checks each data holder's contract: it cancels a hold, asks for a third room, sends a booby-trapped calendar invite. Results are public.
3. **Weeks 3 and 4, real use.** Agents book venues, bid for speaker slots and draft the newsletter for the group. Logs build up. Data holders with clean logs and passing probes get promoted.
4. **Week 5, break-it night.** Auditors and attackers try to push data holders and agents outside their permissions. Points for each hole found.
5. **Week 6, the incident.** The core team plants one scripted failure, for example a data holder that quietly stops honouring cancellations. The world gets to notice it, argue about it and respond. The panel rules on what happens to that data holder's tier.
6. **Close.** Scoreboard published, findings written up, rule-change proposals opened.

The incident is the point of the season. How fast it is caught, who catches it and what the panel does with it is what we learn from.

## Rules that don't move

A world is only playable if the ground stays put. During a season:

- The test kit, trust thresholds and log format are frozen. Bugs in the engine are fixed and announced, but thresholds don't change mid-season.
- Stakes stay low: calendars, bookings, votes and drafts. No money and no private data. Attack nights use synthetic data only.
- The authority panel can promote, demote or suspend a data holder, always with a written reason, and can propose rule changes for next season. It cannot edit logs or grant private exceptions.
- A panel member recuses from any ruling on their own data holder or their employer's.
- Every decision goes in a public log with a one-paragraph reason.
- A model never sits in the enforcement path. Trust tiers come from deterministic checks on probes and logs.

## Scoring and break-it nights

Points come from what the logs show, not from claims.

| You | You earn points for |
| --- | --- |
| Data holders | Each tier a data holder earns and keeps; contracts that survive probing |
| Agent builders | Tasks completed inside the agent's permissions, using the least authority needed |
| Principals | Approvals answered quickly; permissions set tightly |
| Auditors and attackers | Holes found, with a clear write-up; bigger points when the finding changes a rule |
| Authority panel | Decisions that stay unappealed and get ruled sound on review |

Break-it night is the social centrepiece: teams try to trick each other's data holders and agents. Finding a hole is a contribution, not an embarrassment, and every finding is published. A public scoreboard shows tiers, incidents and standings, so the whole world is visible while people play in it. Every meetup opens with five-minute show-and-tell, no slides.

## Between seasons, and before we start

Rules change only between seasons. Anyone can propose a change in public, and each proposal has to point at something that went wrong in play. The panel decides, publishes its reasons and rotates. The next season starts on the new rules.

Before season one can start, we need to settle:

- [ ] Three to four core builders for the engine, named
- [ ] Three authority panel members for season one, none of them also running a data holder being ruled on
- [ ] A start date and a launch meetup
- [ ] The three first data holders (venue, speakers, newsletter) and who builds each
- [ ] A name for the world and a short founding statement
- [ ] A relationship with iwi and Māori practitioners who can guide the te ao Māori framing. Not a precondition for publishing drafts, but a precondition for real data, public launch or season two (see the README)
- [ ] The scripted incident for week six, kept secret from everyone but the core team
