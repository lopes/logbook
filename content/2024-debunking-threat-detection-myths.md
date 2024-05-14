+++
title = "Debunking Threat Detection Myths"
date  = 2024-05-14
description = "Challenging myths in threat detection analytics for enhanced security operations."

[taxonomies]
tags = ["security", "threatdetection", "csirt"]
+++

When I transitioned from CSIRT to Threat Detection, I encountered some "dos" and "don'ts" in writing analytics (myths).  During my ramp-up period, as a curious new member, I followed my instincts without concrete arguments and quietly questioned some of them.  This approach allowed me to form my own perspective (along with supporting arguments), consequently influencing the team's approach to such situations.

In this post, I'll present two common assumptions I encountered and arguments showing they are neither **entirely** correct nor incorrect.  While they may not always be effective, in certain scenarios, they can provide valuable analytics for monitoring critical assets.  Let's delve in.


## Assumption 1: Don't Create IOC-based Analytics
When you become familiar with [David Bianco's Pyramid of Pain](https://www.attackiq.com/glossary/pyramid-of-pain/), you understand that its base consists of Indicators of Compromise (IOCs), which are transient and can be easily altered by adversaries to evade security measures.  Moreover, IOC-based analytics often result in numerous false positives, significantly contributing to alert fatigue in Security Operations (SecOps).

While these assertions hold true, does this mean you should completely avoid IOC-based analytics?  In my view, no.  In specific situations, they can aid in creating effective rules.  In the ["Giving Lower Fidelity More Fidelity" talk](https://www.youtube.com/watch?v=NRY5fKZDGVU) at SANS, Jeremy Johnson demonstrates that IOC-based rules can be beneficial when applied contextually, such as restricting them to critical users or systems rather than applying them universally.

Similarly, as David Bianco highlights in [this talk](https://www.youtube.com/watch?v=ueGZosLD7iE), it's advisable to maintain a curated set of IOCs tailored to your organization, rather than relying on a large collection of generic IOCs from various sources.  With such a list, there's greater confidence in detections, as any association between an asset (user, host, etc.) in your environment and these known, curated malicious IOCs warrants investigation at the least.

Additionally, due to their transient nature, these IOC lists should undergo frequent review (e.g., weekly), removing obsolete entries and incorporating new ones.  I would also establish "high critical" (emphasizing redundancy) IOC lists and apply them more broadly across the environment.  If an internal Cyber Threat Intelligence (CTI) team is available to assist in managing such lists, it's beneficial to have these highly reliable IOCs in place to monitor user interactions.

With a meticulously curated list of IOCs and a roster of critical systems or users, tailored analytics can be devised to detect any communication between malicious artifacts (IOCs) and critical assets. When these lists are accurately maintained, any events of this nature warrant thorough investigation.


## Assumption 2: Reconsider Creating Analytics that Overlap Preventative Controls
In my tenure with CSIRT, we exercised caution when generating alerts for blocked actions, particularly within endpoint solutions.  The question arises: what steps does the CSIRT take if mitigation measures are already in place?  However, it's imperative to acknowledge that adversaries may persist in attempting malicious activities despite consistent blocking, warranting attention.

Moreover, [Ryan Stillions emphasizes](https://ryanstillions.blogspot.com/2014/04/the-dml-model_21.html), *"prevention eventually fails,"* highlighting the necessity of implementing safeguards to mitigate residual risks.  Developing analytics that complement specific preventative controls instills confidence that critical events are not overlooked.

For instance, suppose preventative controls (ACL and bucket permissions) are in place to restrict access to any AWS S3 bucket.  In this case, complementary detective controls can monitor changes made to the ACL and bucket permissions, triggering "Informational" alerts.  Utilizing automation, as discussed [here](@/2024-secops-beyond-tuning-analytics.md), notifications would be sent to the personnel responsible for the bucket to confirm or refute the action.  If unacknowledged, the alert would escalate for further investigation.

As mentioned, this approach applies to specific preventative alerts and is not universally applicable. Therefore, the Detection team must establish criteria to determine suitable cases.  Additionally, CSIRT involvement is essential to configure tools effectively and mitigate alert fatigue.


## Takeaways
Teams often develop their own myths, and newcomers are ideally positioned to challenge them with evidence and reasoning.  This isn't about invalidating previous efforts but rather an opportunity to revisit practices, either validating or retiring them.  Given the evolving nature of technology and procedures, adaptation is key.  Individuals should be prepared to give and receive feedback aligned with the team's objectives.

Prioritizing informational alerts, coupled with simple automations, significantly expands monitoring capabilities, providing crucial visibility into critical assets and preventative controls.  Understanding that IOCs can be effective in certain scenarios while detrimental in others, as well as recognizing the value of detection controls over preventative ones, underscores the importance of scenario-based approaches and adaptability.

Don't settle.
