+++
title = "Friction Between Red Teams and Incident Response"
date  = 2022-10-04
description = "Avoidable friction between cybersecurity teams during Red Team exercises causing stress and burnout."

[taxonomies]
tags = ["security", "csirt"]
+++

I've been working with the incident response (IR) for a few years and more recently when [Red Teams](https://csrc.nist.gov/glossary/term/red_team) (RTs) started trending, I experienced some avoidable friction between both teams I wanted to share.

> **Disclaimer**: This text is based on my own experience and may not reflect the whole scenario of Cybersecurity.  Readers can feel free to send feedback and share experiences.

Analyzing past situations, I divide the RTs into three categories, based on the expertise of their members:

- **Immature**: Composed of script kiddies in poorly established teams running random noisy tests over the network.
- **Outsourced**: Experienced consultants quietly moving over the network exploring vulnerabilities, without a concise roadmap.
- **Mature**: Well-established teams performing tests aligned with the security objectives with in-house or outsourced members (or a mix of them).

In both scenarios, I, as part of the security team, had to deal with being advertised or not of such tests and this is the central point here.

![The Battle of Valmy](/images/painting-valmy-battle.jpg "Battle of Valmy won by General Kellermann over the Prussian and troops of Brunswick in 1792")


## To Warn or Not To Warn, That's The Question

Performing an RT exercise is dangerous for the environment, especially for Immature or Outsourced teams, but no matter the RT constitution, it's **always** stressful for IR Engineers.  The problem here transcends technology: It's <mark>psychological</mark>.  See, [occupational burnout](https://en.wikipedia.org/wiki/Occupational_burnout) is a common word in the cybersecurity area and many professionals are on the verge of it, as well as they're full of attributions, trying to solve problems beyond their field of expertise, which is also frustrating.

When someone decides to run a cybersecurity exercise, usually those involved are calm and feel productive.  It's exciting to do something you love, I know it.  But the problem is that not every person feels the same and it should be considered that other teams have their agenda.  Therefore the test should be disclosed to the teams directly involved in it: Security teams (IR, endpoint, DLP...), as well as IT teams, like server infrastructure.

My point here is clear: The **systems** are going to be tested, not the people --at least not the security team.  Of course, at some point, the people must be tested either, but senior management should take this decision --read: A **real** CISO.  This professional will (or should) look to the teams as a whole and decide if it's the right time and how to analyze the people's behavior.

The team's psychological stress **must** be justified, thus the more secret the test is, the more people affected, and the more RT must be charged for results.  The ideal scenario for me would be stating that all RT exercises would be known by the whole security team like posted in an internal channel.  One or two tests per year would be confidential and comprehensive reports would be written for them, including not only the technological vulnerabilities found but also metrics for the team's performance and subjective points about the quality of actions taken and the people's behavior.  That's because in such tests, not only systems are assessed, but the security teams too.

Without the information on the teams' performance and proper presentation of the results, the stress is not justified, then managers tend to lose reputation within the team, and the friction among RT and other security teams increases.  Having an annual roadmap of tests is paramount here, but this roadmap should consider the results of each test before continuing.  It's no use reassessing a system that was not fixed yet or to send more vulnerabilities to the team that couldn't fix all of the previous vulnerabilities found.  In this case, an alternative should be found, like hiring more people or a consultancy or applying other types of controls --change the approach.  The vulnerabilities should be fixed ASAP and if the team is not being able to do it, something is wrong and must be fixed.


## Beyond The Tests

All tests must have concise reports at least with the vulnerabilities found, so other teams will read this report and define the best approach to fix each issue.  Usually, the tools find lots of vulnerabilities and the RT is responsible to interpret this output and write reports appropriate for the organization, including language, systems/teams names, vulnerability aggregation, etc.

The RT should also consider that many initiatives are on course to fix vulnerabilities found in other tests, so the new report should consider the context where the organization is inserted.  As stated before, if some vulnerabilities are taking too long to fix, the approach should change, and the new report is a good place to state it.  Preferably, this report should be presented at least in two forms: One for managers, with fewer details and more numbers, and the other for Engineers, with all details on how to fix the problems found.

Regarding the security teams' performance on the IR, as mentioned before, I think that it should be precisely measured to justify blind tests (from their point of view).  Here are some ideas for KPIs:

- Time to detect the activity
- Time to identify the incident (triage) and first measures adopted, like onboarding [CSIRT](https://csrc.nist.gov/glossary/term/computer_security_incident_response_team) in the incident
- Time to contain the incident
- Team's behavior: Stressful/calm, followed the playbooks?
- Log quality and network visibility for the IR
- What was missed? Any tool or documentation?
- Incident record quality: Classification, level of details, etc.

Such information will allow senior management and security teams to take actions to improve the triad of People, Processes, and Technology, thus justifying stressing the team.  It will also serve as good preparation for real incidents, granting that the weak points will be fixed to avoid any impact in the future.

With that in mind, it becomes clear that the assessments should follow a cycle like described by NIST in [SP 800-115](https://csrc.nist.gov/publications/detail/sp/800-115/final).  So most of the time, the RT would be preparing, learning, and running tests by other teams' requests --to test a new application, for instance.

Finally, one word on **prioritization**.  The first actions should consider not only the severity of the vulnerabilities (usually measured by [CVSS](https://nvd.nist.gov/vuln-metrics/cvss)), but also a link to the Business Continuity Process should be established, thus prioritizing the assets (no matter if it is people, process, or technology) with more impact on the business.  Usually, bigger companies have a risk area with risks mapped, as well as assets directly related to core business and other quantitative and qualitative properties for them, like [RTO](https://www.kyndryl.com/us/en/learn/rto) and [MTTR](https://en.wikipedia.org/wiki/Mean_time_to_repair).


## Bottom Line

For me, only in this scenario, the RT could operate without the other security teams' knowing.  With that, the friction amongst the teams tends to be reduced, improving the synergy, and moving the security forward.  Burnout, anxiety, and impostor syndrome are not only psychological issues but also cyber risks if they affect the security team.

In this vanity war, the RT members always have a good advantage, because they choose the time (usually the best for them) no matter if they were blocked 99 times.  If they succeed only once, that's enough for applause.

All security teams are important and only management with a security background can understand that and put them to work together in favor of true cybersecurity.  For Sun Tzu aficionados, remember: [the *Art of War* is actually a manual on how to avoid it](https://lithub.com/the-art-of-war-is-actually-a-manual-on-how-to-avoid-it/).

Peace.
