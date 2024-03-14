+++
title = "Improving SecOps Beyond Tuning Analytics"
date  = 2024-03-14
description = ""

[taxonomies]
tags = ["security", "threatdetection", "csirt"]
+++


Last week, I published [a post about the Threat Detection Dilemma](@/2024-threat-detection-dilemma.md): there's no perfect analytic, and when you improve the Precision of a rule, the Recall gets worse, and vice versa.  Indeed, fine-tuning rules is key for a sound Security Operations (SecOps), but this is not the only approach in this direction.  After reading Coinbase's CSIRT blog posts ([1](https://www.coinbase.com/blog/scaling-detection-and-response-operations-at-coinbase), [2](https://www.coinbase.com/blog/scaling-detection-and-response-operations-at-coinbase-pt2), [3](https://www.coinbase.com/blog/scaling-detection-and-response-operations-at-coinbase-pt3)), I decided to compile their learnings with my own thoughts, and in this post, I'll show the result.


## The More Visibility, the More Workload
When we put our monitoring tools in place, like EDR, IDS, and SIEM, it's clear that we increase network visibility, which is paramount for good security.  On top of that, we usually implement preventative controls to block certain actions along with detective controls to provide us insights on suspicious events.

So, the more rules in place, no matter how well-written and fine-tuned they are, the more alerts for Incident Responders to investigate, and this consumes time and cognitive load, thus resulting in stress and fatigue.  As our teams are not as elastic as our analytics (the more analytics, the more analysts), we have to find other ways to tackle this problem.


## Triage
As a former Incident Responder, I can state: Gathering information from different sources to contextualize an alert is stressful and time-consuming.  Even if you know where the information is and own the proper credentials to access it, it's a tiring job.  Considering that at any moment you have to add this contextual information to the ticket, it gets even worse.

So, beyond Threat Detection, the first step that should be taken to improve Incident Response is to give the right context to analysts right from minute zero.  When someone opens a case, common information should be present as enrichments, like a list of previous IP addresses linked to the user, login timestamps, hostnames linked to the user, groups she's part of, etc.

Now, if we onboard users to this triage process, automatically asking them to confirm actions through any bot, we can add important contextual data.  Of course, it doesn't make sense for all alert types, but for when it makes sense, adding such automation reduces the Mean Time to Triage (MTTT) and saves important analyst's time.  But we must add a timeout to this so unresponsive users will make the alert escalate by their inaction.


## Containment
If we have metrics in place for our analytics, we know what are the low and high fidelity ones.  Now, for the high fidelity rules, we can go a step further, flagging the alert as a True Positive and moving the Incident Response automatically to the Containment phase.

In Containment, we can implement some routines to automate part of the tasks, depending on the workflow in place.  We could disable and disconnect user sessions, isolate machines, and add firewall rules, for example.  Quoting [Cliff Stoll](@/2024-book-cuckoos-egg.md), sometimes *“it’s easier to give an apology than get permission.”*

In cases like this, we're not only reducing the burden over the SecOps team, but we're also greatly improving metrics like MTTT and Mean Time to Respond.  Ultimately, we're improving our company's security.

We can see security as a state, and the actions across the environment (internal and external) are constantly pushing our environment to the insecure side.  So we must implement routines that'll automatically move our environment again to the secure side.  These are great examples of such routines.


## Closing Thoughts
Fine-tuning analytics should be the first and not the only step against alert fatigue.  In fact, alert fatigue may be only one of the causes for elevated MTTT and MTTR, two common and important metrics for CSIRT.

As a complex problem, it should be tackled in a multi-layered approach with special focus on alert contextualization and automation.  Contextualization helps especially in the Triage phase, providing insightful data to help the Incident Responder see the big picture and make decisions.  Automation, on the other hand, is useful in the Containment phase because many actions taken during this phase are repetitive and, depending on our environment, easily scriptable.

That's the beauty of working with metrics.  Having Precision and Recall in Detection's side will give us important information to leverage actions in the Incident Response side to achieve better MTTT and MTTR.  But we're not just working for these numbers; better numbers for these metrics are a certainty of better security, our ultimate goal.

{% admonition(type="note", title="Note") %}
For more in-depth information on many of the points presented here, I strongly recommend reading Coinbase's posts.  It's an easy and insightful read.
{% end %}
