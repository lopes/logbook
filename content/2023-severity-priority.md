+++
title = "Understanding Severity and Priority"
date  = 2023-11-23
description = "Uncover optimal Detection Rule settings for consistent, efficient alerts."

[taxonomies]
tags = ["security", "threatdetection", "csirt"]
+++

During the review of Detection Rules, I observed a significant gap – the absence of parameters to define severities and priorities.  Currently, we only assess them when a high-severity alert for a seemingly trivial (and noisy) action triggers, causing phones to ring.  In this review, I delved into these concepts, aiming to define parameters for their proper assignment.  Here, I will share the insights gained from this exploration.

**Severity**, as per the [Oxford Dictionary](https://www.oxfordlearnersdictionaries.com/us/definition/english/severity), denotes "the fact or condition of something being extremely bad or serious.” Similarly, **priority** is described as "something that you think is more important than other things and should be dealt with first" ([source](https://www.oxfordlearnersdictionaries.com/us/definition/english/priority)).

Considering these definitions, it's evident that in Detection Rules, both terms are interconnected.  Therefore, it's essential to consider how they work in conjunction, not merely in isolation, to create consistent alerts.  While, generally, higher severities necessitate higher priorities (where critical alerts must be addressed first), the reverse is not obligatory (less critical alerts can be handled with higher priority in specific cases).

**Severity** should factor in the assets involved in the alert as crucial assets naturally warrant higher severities.  For instance, a reported phishing incident (assuming no user interaction) may have lower severity.  However, the same scenario involving a C-level executive or a Sysadmin typically carries high severity due to the heightened risk to the company.

In contrast, **priority** must consider both the assets in the alert and the severity.  <mark>Priority depends on severity, but severity doesn't depend on priority.</mark>  Therefore, using, for example, severity=high and priority=low is illogical, akin to saying, “we were hacked, but you can handle this later.”

As a **rule of thumb**, priority should be greater than or equal to severity.  Severity, in turn, must consider various factors, including monitored assets, rule precision, risk, and incident impact (materialized risk).

These variables are often overlooked by Detection teams, possibly contributing to the Incident Response team ignoring them and handling alerts in the order they arrive, not by priority.  It's crucial to note that if everything is prioritized, nothing is prioritized.  While it's acceptable to have numerous rules with higher priorities, it's essential to assess their triggering frequency.  For instance, a low-precision rule triggering 10 times a day with priority=high can create chaos in the SOC.  Furthermore, envision five priority=high rules triggering simultaneously: In a genuine attack, chaos is justified; however, if only one alert is legitimate, the team might postpone its response.

{% admonition(type="tip", title="Pro Tip") %}
Consider mapping severity and priority across all rules and adopting a standard (if your Detection system doesn't enforce one) such as `low`/`medium`/`high` or `1`/`2`/`3`.  Additional `info` and `critical` (or `0` and `4` for numeric values) can complement your detections, but only incorporate them with a clear understanding.  A golden rule is: Start simple and improve organically.
{% end %}

Periodic rule reviews should include this evaluation, as analyzing both parameters in perspective provides clarity.  When comparing two rules side by side, it becomes apparent if the assigned values make sense as a whole.  I also recommend creating some form of automation to highlight high severity rules and rules with priority lower than severity.

In essence, aligning severity and priority optimizes alert handling, fostering a more efficient and coherent Incident Response strategy.  Have it in mind.
