+++
title = "MITRE ATT&CK 101: Bridging the Gap"
date  = 2024-01-12
description = "Essential guide to MITRE ATT&CK, bridging gaps with practical insights for cybersecurity."

[taxonomies]
tags = ["security", "threatdetection"]
+++

Around six months ago, I transitioned from the Incident Response to the Threat Detection team.  Taking on the responsibility of migrating almost 300 analytics to our new SIEM posed a significant challenge, and I found great excitement in leading this initiative.

Our previous rules were all mapped to MITRE ATT&CK, and being a detail-oriented professional, I couldn't ignore the fact that some rules were linked with techniques not related to the actual objective.  As a CSIRT Analyst, my knowledge about MITRE ATT&CK was limited to what was strictly necessary to read reports and rules.  However, as a Threat Detection Analyst, I had to delve deeper into it to document rules and provide more context to others.

Despite MITRE ATT&CK being a common topic in the Infosec world, surprisingly few sources provide real context about its history, building blocks, and practical use cases.  Last month, I published [a post](@/2023-mitre-attack-getting-real.md) on this subject, but I felt it lacked an explanation of the basics. This post aims to fill that gap.


## History
MITRE provides an excellent source to learn about the history of ATT&CK.  In their [MITRE ATT&CK Design and Philosophy](https://attack.mitre.org/docs/ATTACK_Design_and_Philosophy_March_2020.pdf) book, they explain:

> ATT&CK was created out of a need to systematically categorize adversary behavior as part of conducting structured adversary emulation exercises within MITRE’s FMX research environment. Established in 2010, FMX provided a “living lab” capability that allowed researchers access to a production enclave of the MITRE corporate network to deploy tools, test, and refine ideas on how to better detect threats. […] The primary metric for success was “How well are we doing at detecting documented adversary behavior?” To effectively work towards that goal, it proved useful to categorize observed behavior across relevant real-world adversary groups and use that information to conduct controlled exercises emulating those adversaries within the FMX environment. ATT&CK was used by both the adversary emulation team (for scenario development) and the defender team (for analytic progress measurement), which made it a driving force within the FMX research.

The framework emerged from a Purple Team exercise, fostering a common language between attack and defense teams. Despite its name, ATT&CK serves not only offensive security but also all Information Security teams, facilitating better communication by using a shared vocabulary.  MITRE ATT&CK is a curated knowledge base and model for understanding cyber adversary behavior, covering various phases of an attack lifecycle and targeted platforms.


## Building Blocks
MITRE ATT&CK is built upon essential concepts crucial for effective usage.  The first is the concept of Technology Domains, representing the ecosystem an adversary operates within.  To date, there are three main domains: Enterprise, Mobile, and ICS (Industrial Control Systems).

Within each Technology Domain, Platforms represent the system an adversary operates within, encompassing operating systems, applications, and service types.  These Platforms set the scope for the main data: Tactics, Techniques, and Procedures (TTPs).

- **Tactics:** Represent the *"why"* of an ATT&CK technique or sub-technique.  It signifies the adversary's tactical objective, the reason for performing an action.  Tactics act as contextual categories for individual techniques, covering standard notations for activities like persistence, information discovery, lateral movement, file execution, and data exfiltration.  Tactics are treated as *"tags"* within ATT&CK, associating a technique or sub-technique with one or more tactic categories based on the achieved results.
- **Techniques:** Illustrate *"how"* an adversary achieves a tactical objective through actions.  Techniques may also signify *"what"* an adversary gains from performing an action.  Multiple techniques may exist within each tactic category, offering diverse ways to achieve tactical objectives.  Similarly, multiple distinct sub-techniques may exist under a technique, reflecting various ways to execute it.
- **Procedures:** Denote the specific implementation adversaries employ for techniques or sub-techniques.  For instance, a procedure might detail APT28 using PowerShell to inject into `lsass.exe` for credential dumping by scraping LSASS memory on a victim.  Procedures are documented in ATT&CK as observed in-the-wild uses of techniques, found in the *"Procedure Examples"* section of the technique and sub-technique pages.section.

{% admonition(type="note", title="Note") %}
Other concepts in ATT&CK, such as Mitigations, Sub-Techniques, and Data Sources, exist but are beyond the scope of this text.  References provided can guide you to explore these concepts further.
{% end %}

### Abstraction
MITRE ATT&CK serves as a mid-level model, bridging high-level concepts from other models such as the [Pyramid of Pain](https://www.sans.org/tools/the-pyramid-of-pain/) and [Cyber Kill Chain](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html) with low-level concepts like tools, exploits, and vulnerabilities.  Understanding the level of abstraction within which this model operates is crucial to realizing its potential value.

Let's delve into an example: Suppose you have developed analytics to monitor network scans, categorizing it under the Tactic Reconnaissance and Technique Active Scanning.  It's important to note that ATT&CK offers a broad taxonomy for Tactics and Techniques but does not extend to Procedures.  As previously discussed, <mark>detailing procedures is impractical</mark> due to the multitude of tools and methods available for such tasks.  In this context, your rule might specifically cover Nmap scans.  However, when utilizing this data for a coverage map, the Technique Active Scanning is considered covered, even if the adversary deploys a Python script to scan your network, potentially going unnoticed by your rule.

This aligns with [Katie Nickels' assertion](https://www.youtube.com/watch?v=bkfwMADar0M&t=1062s) that <mark>achieving perfect coverage is unattainable</mark>.  Even when using qualitative metrics to express confidence in a rule, it's essential to recognize that ATT&CK cannot provide a granular mapping of covered Procedures.  Nevertheless, this limitation doesn't imply avoiding the model; rather, it emphasizes the need to consider this aspect when analyzing your scenario, ensuring a comprehensive understanding.


## Using MITRE ATT&CK
Now that you have a basic understanding of what MITRE ATT&CK is and how it's structured, the next step is to explore where you can apply it and what benefits you can derive from it.  MITRE provides valuable guidance in the [Getting Started with ATT&CK](https://www.mitre.org/sites/default/files/2021-11/getting-started-with-attack-october-2019.pdf) book, offering insights into four key areas: Threat Intelligence, Detection and Analytics, Adversary Emulation and Red Teaming, and Assessments and Engineering.

The authors present ideas for using ATT&CK in these areas at three proficiency levels.  The first level is designed for teams initiating their journey with ATT&CK, the second for mid-level teams seeking maturation, and the third for advanced teams.  Each area has a dedicated chapter in the book, and I recommend reading them in the suggested sequence.  The content is presented in a clear and straightforward manner, making it easy to comprehend without the need for summarization.

{% admonition(type="note", title="Note") %}
In [the other post](@/2023-mitre-attack-getting-real.md), I share practical ideas for implementing ATT&CK.  Check it out!
{% end %}

### Bias
The Getting Started book includes a notable section on the bias of analysis:

> Compare your results to other analysts—Of course, you might have a different interpretation of a behavior than another analyst. This is normal, and it happens all the time on the ATT&CK team! I’d highly recommend comparing your ATT&CK mapping of information to another analyst’s and discussing any differences.

Forgetting to map a technique in a rule or mapping a technique that other analysts wouldn't use haunted me during my early days as a Threat Detection Analyst.  I addressed this issue by seeking peer reviews for my choices, which proved beneficial when others agreed with me or added techniques I had overlooked.  <mark>There are few incorrect answers in this mapping endeavor</mark>, and with a solid understanding of Infosec and careful reading of the Tactics and Techniques descriptions, you'll be on solid ground.  <mark>Incorporating peer reviews</mark> into the process will further enhance the quality of your mapping.  Additionally, MITRE provides mappings for APTs, allowing you to use them for learning or comparison purposes as an exercise.

### Mapping Overlay
MITRE ATT&CK was developed to facilitate the alignment of offensive and defensive teams by establishing a common language.  This aids in enhancing communication across teams.  MITRE offers a valuable tool called [Navigator](https://mitre-attack.github.io/attack-navigator/), which visually represents mappings as layers.  Navigator has the capability to merge layers, facilitating coverage analysis.

Consider a scenario where your Threat Detection, Cyber Threat Intelligence, and Vulnerability Management teams utilize ATT&CK layers.  By loading these layers into Navigator, an Infosec manager can easily identify gaps between current analytics, vulnerabilities, and potential adversaries targeting the company.  This adds another layer of insight.

### Threat Hunting
MITRE ATT&CK proves beneficial in the realm of threat hunting, and [MITRE's TTP-Based Hunting](https://www.mitre.org/sites/default/files/2021-11/prs-19-3892-ttp-based-hunting.pdf) provides comprehensive details on the subject.  The authors define Threat Hunting as “the proactive detection and investigation of malicious activity within a network.”  A "hunt team" is a group dedicated to conducting hunts on a given network, utilizing data and tools to detect and investigate suspicious activities.

The referenced book offers valuable insights and includes a literature review on Threat Detection terminology, providing readers with a foundational understanding.  It introduces the TTP-Based Detection approach, which focuses on characterizing and searching for the techniques adversaries use to achieve their goals, rather than searching for specific tools and artifacts.  The methodology is depicted as a "V" diagram, with the left side dedicated to characterizing actions and the right side focused on creating analytics.

While I haven't personally employed this methodology yet, I find it intriguing.  Based on understanding the problem, defining hypotheses and requirements, and initiating further investigation, it appears to follow a scientific approach.  It's definitely on my radar for future experimentation.


## Takeaways
MITRE ATT&CK is a robust model, or framework if you prefer, that proves invaluable in the realm of threat detection.  Its widespread adoption implies seamless integration with tools and other teams.  However, unlocking the true value of ATT&CK requires a deep understanding of its purpose and the problems it aims to solve.  Positioned as a mid-level model, ATT&CK facilitates communication and integration among Infosec teams.

Some crucial advice applies in this context.  <mark>Avoid attempting to implement countermeasures against every technique, and recognize the impracticality of achieving perfect coverage over a technique.</mark>  If you've grasped the basics, you'll comprehend why.

Starting simple with MITRE ATT&CK is perfectly acceptable, supported by available materials (as previously presented in this post).  However, from day one, it's essential to have a clear understanding of how to leverage this data.  Mere mapping without practical application may lead to a sense of futile effort.  This, in turn, could result in the creation of incorrect mappings, leading to misinformation -—a situation more detrimental than a lack of information.  Actively use this data to define and prioritize actions, incorporating it into regular syncs with the team.  While there's no harm in starting small (I, myself, am a huge fan of MVPs), maintain consistency and robustness in your approach.  Map and actively use the data; you'll find it beneficial, and improvements will naturally follow.
