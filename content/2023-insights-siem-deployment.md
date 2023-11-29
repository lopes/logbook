+++
title = "Insights into Effective SIEM Deployment"
date  = 2023-11-29
description = "Strategies and tips for successful SIEM deployment."

[taxonomies]
tags = ["security", "logging", "siem"]
+++


After successfully deploying two SIEMs (one as a Team Leader and the other as a Tech Lead), I've decided to compile the lessons I've learned. Deploying a new system is challenging, and as SIEMs interconnect different systems while implementing features that may be used across various teams, this task tends to be complex.

Here, I share the method I extracted from both experiences along with some insights and tips. In general, you should start with a discovery to help define the scope. Then, pay attention to the two most important frontlines: Data ingestion and rule creation. All of this must be accomplished while keeping track of activities and carefully moving the system to production to reduce stress and avoid rejection from SecOps. Let's get started!


## Discovery
The first step is to run a discovery on your environment. If you already have a SIEM-like tool, you already have a list of Data Sources to ingest and a couple of Detection Rules running. If you don't have such a tool, you will/should have a list of requirements or use cases to implement.

The next step is to define the system goals. While SIEM is primarily aimed at Detection, it can also help in Compliance, Audit, Vulnerability Management, and other related areas. Therefore, you must define a scope that comprises Data Ingestion and Rule Creation, focusing on solving problems in areas like Security Operation, Governance, or Fraud. Defining some use cases may help you a lot here, and if your company lacks maturity in this field, you can possibly get a list from partners or vendors.

{% admonition(type="tip", title="Pro Tip") %}
Start with a minor scope and increase it as the project advances, like an MVP. There's no right or wrong here, so use your expertise to set a small, yet challenging, scope to start and possibly design the next phases on which the systems will grow in size and importance.
{% end %}


## Scope
After the Discovery phase, you should have a clear understanding of your scope. Make sure to document it and validate it with the management and the personnel accountable for the project. As I said, I like the MVP idea, so I'd break a project like this:

1. **Adoption:** Define requisites, ingest the first Data Sources, implement/migrate the first rules, review the work, implement best practices. Create documentation and a knowledge base to help newcomers.
2. **Enhancement:** Ingest other important Data Sources and implement more rules, this time based on CTI reports, Threat Hunts, audit/compliance, or any library (like [Sigma](https://github.com/SigmaHQ/sigma)). Allow more teams to work with the tool.
3. **Master:** Review the use cases, fix any deviation from best practices, ingest even more data, implement more detection rules, and onboard more teams to the tool.

{% admonition(type="tip", title="Pro Tip") %}
It's common for people to ask things out of scope. For each case you, as the Tech Lead or Project Manager (hope not to be both at the same time for sanity reasons), must evaluate if it makes sense to change the scope or to say “no". Mainly during the deployment, when you must put a new tool in operation, clearly defining a scope and sticking to it will help you focus on the most important tasks to accomplish the projects’ goals and separate the phases based on their Definitions of Done.
{% end %}


## Data Ingestion
<mark>Every SIEM is as good as the data it ingests</mark>, and not always more data is better. As everything else will rely on this data, your work must start here. Handling Data Sources (aka, Log Sources) in groups greatly helps implement the phased deployment, where the most important Data Sources are ingested first and the others are ingested in later stages.

{% admonition(type="tip", title="Pro Tip") %}
Vendors use to say the more data the better, but of course it increases their profitability and not necessarily in more security. Have in mind that it's fine to filter data and usually it avoids useless data in the system, thus speeding up things. As the project moves to next phases, you can change such filters to ingest more data, but it'll happen on demand.
{% end %}

The Data Source list and prioritization should follow any rule (or a combination of them), like a Business Impact Analysis, directive from senior management, CTI reports, SecOps Analysts’ feedbacks, and other related inputs. An important idea here is to create a “V1” and ask for feedback from other stakeholders.

If you have a SIEM in production, the Data Sources related to the rules you chose to migrate will become prerequisites for the new system. Despite having to control the project in some tool (ex.: Jira 😰), I like the idea of using a spreadsheet here because it's easy to catalog, to track, and if you use a cloud-based one, you'll enable parallel work on it. Simple, easy, and elegant; just make sure to be the owner and curator of such a tool because people use to create a real mess without a benevolent dictator (control freak 🤪).


## Rule Creation
You'll only be able to start implementing the rules when the Data Sources they use are properly ingested. If you already have a SIEM and some monitoring rules in place, I strongly suggest that you enumerate them in a spreadsheet (preferably the same used as a Data Source catalog, but in a different tab) and analyze one by one to decide if you will migrate them.

No matter the length of this list, I strongly suggest you to start your rule creation from it, because they are actual security controls you have in place, and if after analysis you found them important, you don't want to let your guard down by inadvertently disabling them. Also, before start creating new rules and adding more complexity to the environment, it's safer and easier to have everything in order.

{% admonition(type="tip", title="Pro Tip") %}
Some existing rules, despite being weird and useless, may exist for any reasons, like audit and compliance. So, before deprecating anything, make sure to validate your decisions. Preferably, this task (like many others) should be accomplished in pairs and possibly follow a validation process. The [Linus Law](http://www.catb.org/~esr/writings/cathedral-bazaar/cathedral-bazaar/ar01s04.html) fits pretty well here: *“Given enough eyeballs, all bugs are shallow.”*
{% end %}


## Track Data and Keep People Synced
Having the spreadsheets and the tools to follow up the tasks will let you know what's going on and who's doing what. This is very important because (luckily) you have some different people and teams working on the project and some of them may depend on each other (the team writing rules will depend on the team responsible for data ingestion, for example).

I don't like the idea of creating meetings to sync because statuses can be updated async, but it's crucial to have a weekly meeting to join all people working on the project to put everyone on the same page. Despite having tools to communicate and maybe dedicated channels to expose achievements and pain points, some people just miss new messages or misinterpret written information. So, syncing on a weekly basis for 30 minutes may save much time in the project. Just make sure to have a host for this meeting (maybe yourself), to invite only people involved in the project, and to take notes of insights, highlights, pain points, or blockers to take further action.


## Go Live
Usually, a SIEM will be used by some sort of SOC Analysts (L1, L2, L3…) and routinely such a team already exists with their own schedule and problems. Be sure to create a direct channel to them and take into consideration their concerns and ideas in the project, especially when you start moving changes to production. It's important to create a deployment agenda along with them considering shifts, holidays, and how to handle/communicate unexpected events.

Consider that a SecOps environment is often stressful and a change with this magnitude may raise the level of stress which could cause the team to reject the new tool. We don't want it, so consider what I said and more:

- Focus all changes in the early week (Monday or Tuesday) in the morning, because most people will be available during all day, including you.
- Have a rollback plan in your sleeves to use in case of any problem, like a surge of alerts.
- Be sure to communicate with the team right at the moment you implement the changes, be available to them, and make sure to follow the agenda you defined previously.


## Last Words
Of course, there are many other important things to consider, like monitoring the SIEM itself, reviewing the work, and documenting the most important parts. But I listed here my key points to consider during a project like this. In my experience, a project like this has many stages of adoption, and the deployment, as the first of them, is the most important IMO.

In a good deploy, you should start getting good results right in the first month. As a SIEM with strategic and well-configured Data Sources greatly increases the visibility over the environment, usually you'll find misconfigured applications, frozen servers, users trying to bypass controls, and adversaries eventually. Just remember that the SIEM is just the beginning of a new process, so after detecting anything, you'll have to take action, and that's why a SOAR is a nice complement to SIEM. But Incident Response is another story.
