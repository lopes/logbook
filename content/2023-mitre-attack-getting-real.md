+++
title = "Getting Real About MITRE ATT&CK"
date  = 2023-12-14
description = "Understanding MITRE ATT&CK and using it in your favor."

[taxonomies]
tags = ["threatdetection", "security"]
+++


According to the [Cambridge Dictionary](https://dictionary.cambridge.org/us/dictionary/english/framework), a framework is "a supporting structure around which something can be built."  This implies that a framework is not the end goal but rather another tool within your project to achieve specific outcomes.

Now, let's dive into Infosec.  As per [Rapid7](https://www.rapid7.com/fundamentals/mitre-attack/), the [MITRE ATT&CK](https://attack.mitre.org/) framework is used "to document attacker tactics and techniques based on real-world observations."  From this, we can deduce that ATT&CK aids in identifying gaps, addressing queries, fostering team collaboration, prioritizing actions, and other related tasks based on mapped TTPs.  In essence, the focus is on questions like "how's our coverage?" or "what tasks should we prioritize?" rather than a blanket use of MITRE ATT&CK.

Therefore, ATT&CK alone isn't a magic solution for your organization; it needs to be applied within a specific context to make sense and deliver tangible results.  Let's dig into this idea.

![Threat Detection Radar](/images/threat-detection-radar.gif "Green radar screen showing some detections")


## Implementation Insights
I view context here as dimensions, and it's up to you to define which ones you want to apply MITRE ATT&CK to, enabling a better understanding of your situation and where action is needed.  Examples of dimensions include Detection Rules, vulnerabilities, simulated exercises, adversaries (APTs), and CTI reports.

During Sp4rkCon 2019, Katie Nickels offered [valuable insights](https://www.youtube.com/watch?v=bkfwMADar0M), encouraging Infosec professionals to apply ATT&CK with what they already have, urging a start with simplicity to achieve quick initial results.  The key takeaway from Nickels’ talk is to use ATT&CK data within your existing scenario, avoiding impractical scenarios with elaborate tools and unrealistic data (considering momentum).

I stress the importance of not just mapping but actively using MITRE data.  Mapping is crucial, but without utilization, things become outdated quickly, leading to disengagement within the team.  I've witnessed this when mapping Detection Rules in ATT&CK; without practical use, it felt like time wasted as each review revealed numerous incorrect mappings (often due to copy-and-paste errors).


## Enhancing Threat Detection
Threat Detection and Response (TDR) teams can leverage MITRE ATT&CK within specific contexts.  As mentioned earlier, showcasing which TTPs are covered by each Detection Rule is beneficial.  Taking it a step further, assigning scores to the rules provides insights into both areas that need improvement and overall performance in TTPs.  💡

Moreover, the Cyber Threat Intelligence (CTI) team can integrate TTPs from MITRE with adversaries and the likelihood of exploitation.  Infosec leaders can use such mappings to identify gaps and decide on prioritized actions for upcoming sprints/cycles.

{% admonition(type="tip", title="Pro Tip") %}
MITRE Navigator is an excellent tool for visually representing these mappings.  Invest time in automating data extraction and use it to create layers (e.g., one for TDR and another for CTI).  Overlay this data in Navigator to streamline the analysis process.
{% end %}


## Final Thoughts
While MITRE ATT&CK is valuable, its true worth emerges when properly contextualized.  Choose the dimensions for mapping, catalog data with ATT&CK information, and automate extraction and processing.  Start simple, start now, as an MVP.

Gain a clear understanding of using ATT&CK data and automate extraction as much as possible to maintain team engagement and reduce the burden of working with this data.  MITRE ATT&CK is a framework that adds value when integrated with your data to answer questions you might not even know.

Lastly, compile the results and share them with your colleagues and leaders.  This work can contribute significantly to increasing Infosec maturity within your company.
