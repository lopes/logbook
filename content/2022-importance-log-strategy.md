+++
title = "The Importance of Logging Strategy"
date  = 2022-10-09
description = "Streamlined planning and retention practices for better logging."

[taxonomies]
tags = ["security", "logging", "siem"]
+++

Logs are a key part of successful security or IT plan because they are an outstanding mechanism to diagnose many types of incidents. That's why every corporation should have a strategy to define what logs will be tracked and for how long. Usually, different log sources overlap data and many systems generate lots of useless logs by being too verbose.


## Planning

A good practice would be grouping the systems and defining what each group can inform, like:

- **Routers** and **L3 switches**: ARP and interface information, threshold, incoming/outcoming bytes.  It'll use mostly for availability purposes, but such devices have the capability of using [network flows](https://en.wikipedia.org/wiki/NetFlow), a subject for other texts.
- **Operating Systems** (OSes): Logged users, services running, L4+ connections, installed software, hardware profiling. Useful for IT infrastructure and forensics.  Like network devices, some OSes can send *network flows* either.
- **Services**: Allowed/blocked connections, file hashes, URLs.  In general, these logs are deeply related to each service and greatly differ from each other.
- **Applications**: Logged users, actions triggered...  The developers will have to create the code to log actions.

Since it's humanly impossible for someone to keep reading all these kinds of logs, they should be centralized in a specific tool.  Security Information and Event Management (**SIEM**) are systems specifically designed for that purpose, but they're far from cheap.  Even with open-source options, not every company can buy (or rent) servers with enough capacity to process billions of data per second.

To make better use of such tools (and ultimately, of money), teams should pinch the main devices and applications that are relevant for their work --at least for the beginning, then they can expand.  But even pinching those assets, the logs should be also pinched.  Teams may not want to receive **every** log from the servers, like using a `DEBUG` facility in [Syslog](https://en.wikipedia.org/wiki/Syslog).  Instead, they could find it more interesting to filter what types of logs each asset will send, like selecting specific [Event IDs](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/) in Windows and selecting the facilities and severities that Syslog will send.  That reduces not only the sizing of SIEM but also filters network noise, thus speeding up the queries and the incident response, of course.

### Retention

Keeping a high number of logs consumes lots of space, so there should be configured a maximum period to keep them because the storage is limited.  This retention period should consider first the laws the company has to comply with, like [SOX](https://en.wikipedia.org/wiki/Sarbanes%E2%80%93Oxley_Act).  But security-wise, I'd say that teams need at least 12 months of log retention in general (some exceptions may apply) because this time frame is good enough to understand the system's baseline and provides enough data for responding to incidents.

Of course, more than one log retention period can be defined, but I think that 12 months should be the minimum --for security purposes, at least.  Nowadays, cold storage can be used to store older and less frequently accessed logs.  Tools such as [AWS S3 Glacier](https://aws.amazon.com/s3/storage-classes/glacier/) are awesome for this purpose and this initiative has the potential to allow teams to store logs for longer periods at lower prices.

### Time

Time is crucial for logging, so teams must pay close attention to it.  First, it is essential to have all system clocks synced with the same source, through [NTP](https://ntp.br/), for instance.  Then, the timezones must be properly configured on each device and the team must make sure that the timezones are sent within the log.

See, when an incident occurs, the IR team will try to remount the steps chronologically.  So, if the clocks are not synced or if some OS is using a different timezone, it could mess up the analysis leading the team to inconclusive reasons or misleading the results.

### Protocols

The team should be the best to use open log protocols or proprietary log protocols instead of creating their own.  Syslog for instance have already its header fields defined, so developers are burdened only to create the payload.  Since other applications already recognize such protocols, when sending logs to a SIEM for instance, it'll automatically parse the header.  Usually, programming languages have their implementations of these protocols, like the [Syslog module for Python](https://docs.python.org/3/library/syslog.html).  So, do not reinvent the wheel.


## Doing

With such a structure, when handling, for instance, a performance degradation incident, the engineers could start by analyzing application logs:

- What users are currently logged in?
- Did someone trigger some CPU/memory/disk-heavy routine?
- Any unusual activity, like a user running the same function many times?

Then, the analysis could move to service logs:

- What's the user-agent for each logged user?
- Any user logged from unusual geolocation?
- Is any high number of connections allowed or blocked for a single client?

From OS:

- Is any system degradation related to CPU, memory, or disk?
- Is the hardware OK?
- Is any patch applied or service running that could impact performance?

Finally, from the network:

- Any relevant number of interface errors?
- Is the interface threshold higher than it is capable?
- Which users last logged in?

In the same scenario, if the team thinks it's very likely to be a classic TCP [D]DoS, they could start the investigation from other points, like from the network devices or the OS.

Architecting a logging structure is not an easy task and requires a profound knowledge of the environment.  But the thing is that having all of those types of logs will give the team a complete picture of the scenario, allowing them to find the root cause more assertively.  In other words: It tends to save money and time in the future.
