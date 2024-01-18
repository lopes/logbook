+++
title = "Automating Incident Response"
date  = 2022-10-25
description = "Tackling log centralization, SIEM operationalization, and IR automation."

[taxonomies]
tags = ["security", "logging", "siem", "automation"]
+++


Automating tasks in Incident Response (IR) is key to reduce the impact of incidents. Although it seems as easy as start writing scripts, in my experience, this objective must be tackled in a more scalable and robust way that encompasses security, data science, and software development.

![Breezing Up (A Fair Wind) by Winslow Homer, 1876](/images/paiting-breezing-up.png "Four boys sailing with an old man in a boat")

## Stone Age: Path to SIEM

> In the beginning, was the logs, and the logs were inside the systems, and the systems were decentralized.

When a company hasn't yet defined an approach to deal with logs, this quote should be the reality.  But when the necessity for log management arises, it usually involves log centralization, and the team rapidly discovers that it is neither an easy nor cheap task.

It's not easy because some systems may require agents to send the logs and centralizing the logs is just the tip of the iceberg.  Logs need to be parsed, normalized, indexed, and only then stored in both raw and parsed forms.  The fact is that it will require some ACLs and firewall rules to get the log in to the log server and this server itself needs to have the proper processing, memory, and storage capacity to be able to meet the business need.

It's not a cheap task because the software designed for that purpose (Security Information and Event Management - **SIEM**) is usually expensive --very expensive in some cases.  Although there are open-source options, the hardware/instances needed for processing tons of data (and here I'm talking about many GB of logs per day) requires investment and, of course, people to implement the solution and to keep things working.

**In my experience**, companies usually allocate a budget for this purpose for security or compliance reasons.  Logs are a key part of Incident Response (IR) because they allow Engineers to investigate the underlying systems in many ways, increasing the chances of finding the root cause for incidents and even to discover more incidents.  Nonetheless, the company may be subject to any law that requires storing some logs for a certain period.

The last reason (compliance) is the easiest to justify budget because it usually implies penalties for the company if it's not compliant.  The second (security) is harder to justify, especially if the company had no prior major security incident.  Despite that, security, in my opinion, will be the one that will win the most when the solution is up and running --consequently, security teams tend to be the heavy users of such a tool.


## Bronze Age: Strengthening the Foundations

Having a SIEM running and receiving logs is far from the end of the road because although it's a huge step, the work is only beginning --as affirmed by [MITRE](https://www.mitre.org/news-insights/publication/11-strategies-world-class-cybersecurity-operations-center), "SIEM takes a day to install and a year to operationalize".  After the team understands the technical parts of the tool, Engineers should realize that they have to better understand the logs that are being sent to SIEM and define a baseline, like what are the systems that MUST send data to SIEM and those that should NEVER send data.

Eventually, this task will advance in granularity, so the team will start to filter logs in the sources to avoid garbage in SIEM, thus making better usage of resources.  This refinement not only keeps the budget under control but also tends to improve the performance of SIEM because the more data is ingested, the more resources (hardware) searches will require.  As the number of data feeds increases, the team will have to find ways to manage them, like grouping or tagging them, creating patterns for naming each log source, and then writing alerts for log sources that stopped sending data.  Finally, log sources will get cataloged with enough metadata to manage them.

> Knowing that the <mark>SIEM is as good as the data you feed it</mark>, is gold.  Having a SIEM with filtered logs and sampled flows from many sources is better than receiving all logs and flows from a few sources.  The magic of this tool is to correlate data and the more varied the log sources, the better.  Remember: Garbage in, garbage out.

Advancing in the tool's best practices, the team will start writing faster searches and tune former searches to increase the speed of them.  This task can include also extracting more data from logs because searching on indexed data tends to be faster than searching on raw data.  Queries will be then used with predefined searches (available to Engineers) and as "engines" to create monitoring dashboards.

{% admonition(type="note", title="Note") %}
Usually, SIEM queries are composed of filters and some techniques can be applied to accelerate the response, like filtering first the log sources/indexes that are relevant for that query, specifying users and other data already normalized, and avoiding filters with regular expressions (that will run over raw data).  The objective is to increase the filter granularity from less specific to more specific so that the response tends to be faster.
{% end %}

Correlation rules will be revised to disable those out of scope (like those aimed on tools not owned by the company) and others will be developed to improve detection.  This step includes improving the method for creating new rules, like sending findings to testing queues instead of production queues.

To advance in all of these topics, electing a SIEM champion is key to success because this person will dive deep into the tool, learn its basics and best practices, and create processes to keep the environment performative, stable, sustainable, and safe.  This person will be also responsible to manage the playbooks to handle each type of case identified by the SIEM.  These playbooks are an important part of cybersecurity operations because all incident handlers should refer to them when responding to incidents, thus making the handling more homogeneous.

> Although the SIEM champion will manage the playbooks related to the correlation rules, every CSIRT member should be responsible for keeping them up-to-date.  A good strategy here is to create unique identifiers for the correlation rules and name the playbooks with those IDs, making the association straightforward.


## Iron Age: Implementing SOAR

With the SIEM implemented, receiving data from important sources, transforming data into information, and with Engineers knowing how to handle each alert raised, the team will reach a good level of maturity, ready to move consistently forward.  With this foundation, the team will advance in automation to make the IR faster and easier with a Security Orchestration, Automation, and Response (SOAR).

{% admonition(type="note", title="Note") %}
[Understand](https://www.rapid7.com/solutions/security-orchestration-and-automation/) **automation** as the process of executing security operations-related tasks without human intervention (task-based) and **orchestration** as a method of connecting security tools and integrating disparate security systems to streamline the process and power automation (process-based).  It means that orchestration makes use of automation.
{% end %}

This improvement will go atop each case raised by SIEM, then make use of previous achievements to go even further.  As the team increases its network visibility, more alerts tend to raise and Engineers will have more cases to investigate, so the workload tends to increase.  Having more tickets is challenging, so if the team does not automate some tasks, the need for more Engineers will grow forever.  It should be also stated that more people do not necessarily mean speeding up the IR because some tasks could take longer to run due to the lack of integration of tools.

The SOAR covers this gap by bringing automation and orchestration to IR, thus speeding up the process and keeping the need for new Engineers under control.  It's important to notice that SOAR will only take actions for cases identified by SIEM or tickets manually opened --but since the automation will require certain fields to run, it tends to be more effective on automatic detections.  Also, if the finding arrived through SIEM, it is certain that evidence was collected, allowing the team to investigate the issue.  On the contrary, for manual notifications, there is no guarantee that the evidence is there --and if don't, it must be fixed if the incident is confirmed: new log sources or more verbosity for logs, maybe.

While the SOAR is aimed at automation, one of its "side effects" is to substitute the ticket system for IR response.  Since this tool already comes with an internal ticket system (which is easier to implement compared to automation), the security team can migrate from the IT ticket system to this one.  The advantages are many, but to exemplify some of them, this ticket system (the good ones) usually aggregates repeated tickets (raised alerts), thus decreasing the administrative burden of ticket management, and it segregates IR data to the rest of IT, adding another security layer: Incidents are usually handled by collecting some confidential data and if this data is stored with other less sensitive data, the probability of leak increases.

All this structure tends to improve the IR, but you need KPIs to properly measure it and present the results to senior management, thus justifying the investment.  KPIs can be extracted from the incident process phases, like:

- **Time to detect**: Tends to reduce with the deployment of SIEM and performance improvements in this tool.
- **Time to engage**: A SIEM champion, trained personnel, and well-defined alerts tend to reduce it.
- **Time to contain**: Usually automation in SOAR could help reduce it for many types of incidents.
- **Time to respond**: Trained personnel with good SOAR are key for this.

Other KPIs are important and encompass many tools, like:

- **True/False positive ratio**: When the SIEM is deployed this ratio tends to be high, but as the team adjusts rules and data feeds, properly filling deny and allow lists, this ratio tends to decrease to acceptable values.
- **The number of incidents detected**: The more log sources and correlation rules, the more incidents tend to be found --as the team increases the network visibility, the minor changes made by other teams will raise as tickets and will require investigation.

At this point, the environment should be well monitored, gaps are known and addressed, the team knows how the company network behave and has the tools (SIEM, SOAR, playbooks) to investigate and respond to the issues identified.  Also, when the environment evolve, the team will have a strong foundation to grow together without breaking anything or anyone.  It's a matter of planning and following the plan.
