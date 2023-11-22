+++
title = "Effective Detection Rules for Network and Port Scans: Implementation Strategies"
date  = 2023-05-30
description = "Mastering Effective Network and Port Scan Detection: Strategies, Implementation, and Rule Creation for Optimal Security."

[taxonomies]
tags = ["security", "threatdetection", "siem", "networking", "logging"]
+++

**Active scannings** are part of the initial phases of an attack, as defined by [MITRE](https://attack.mitre.org/tactics/TA0043/).  Close monitoring of these scans can detect threat actors and prevent incidents from causing significant impact.  Although relatively straightforward, implementing such alerts with a low rate of false positives is challenging.  In this article, I will discuss the reasons behind this difficulty and present effective approaches to tackle the problem and create accurate detection rules.


## Scans
It is important to keep in mind that there are at least three types of scans: **network scans**, **port scans**, and **vulnerability scans**. Network scans aim to identify active hosts within the network, while port scans attempt to determine open ports for each host, indicating the services running on those machines.  Vulnerability scans can be used to discover the applications utilizing these ports and potentially identify vulnerabilities for further exploitation.

Although other scans, such as SSH port scans, exist, they are highly specific and typically require particular monitoring rules.  For the purpose of this discussion, the focus will be on <mark>**network and port scans**</mark>.  Many people mistakenly mix them (network and port scans) in detection rules, leading to numerous false positives or, worse, false negatives.

Here are some key differences between them:

- Network scans usually target multiple network addresses, whereas port scans can be performed against a single IP address.
- Network scans can be portless, employing protocols like ICMP, while port scans are tied to port protocols such as UDP and TCP.
- Although both scans can be executed simultaneously using tools like Nmap, port scans tend to take longer to complete, often prompting separate execution.
- Even when both scans are executed within a single command, the typical approach involves initiating the network scan first to detect hosts and in parallel run port scans for each identified host.


## Strategy
Quoting **Sun Tzu**, *"if you know the enemy and know yourself, you need not fear the result of a hundred battles."*  This quote perfectly applies to the context of network traffic, which behaves differently based on factors such as devices, protocols, applications, users, and asset usage.  Understanding these nuances explains why directly implementing detection rules found on the internet into production tends to yield ineffective results.

Creating effective rules for detecting network and port scans requires **extensive observation** of the network and leveraging expertise to identify patterns.  Depending on the network's size, this can be a challenging task.  In such cases, it is advisable to employ the **divide and conquer** approach: Rather than monitoring all internal networks (typically accomplished through a perimeter firewall), start by monitoring specific subnets, such as a VPN subnet or a DMZ.  These subnets often exhibit more focused traffic, enabling easier identification of common protocols and applications.


## Implementation
Ideally, a detection rule should be established per subnet.  This approach allows for setting thresholds that closely align with common usage patterns, facilitating the detection of anomalous behavior at the earliest signs of deviation.  However, managing a large number of rules can be challenging, particularly when network topology changes necessitate the deprecation or creation of new rules.

From my experience, it is possible to develop rules with a higher level of abstraction across the network.  These rules encompass multiple subnets but require clearly defining protocols, source and destination parameters, and balanced thresholds.  Naturally, these thresholds will be more lenient compared to those employed in specific rules.  However, there are no shortcuts when it comes to this process.  Trial and error remains crucial:

1. Set a threshold based on observations and experience.
2. Test the rule using that value (preferably by executing a scan).
3. If no alert is triggered, decrease the threshold and test again.
4. If there are numerous false positives, establish a looser threshold and test again.

The testing process should continue until there are no **false negatives** and an acceptable number of false positives.  It is worth noting that such rules often require a backdoor -—a list of intentionally allowlisted hosts that trigger the rule for valid reasons (e.g., vulnerability scanners used by the team and monitoring systems).  To handle this, a mechanism must be developed to exclude these hosts (typically based on IP addresses) by employing an allowlist.  Although allowlists are crucial for avoiding false positives, they should be used judiciously, as they can pose risks if a threat actor obtains an IP address within that range, rendering the system blind to their activities.


## Wrap-up
You can implement as many scanner rules as necessary, but it is important to ensure they complement the overall set of rules.  This approach avoids overlapping rules that monitor the same assets in the same manner, simplifying maintenance, and optimizing resource utilization.

When referring to complementary rules, consider (for instance) having network and port scanner rules to monitor the internal network, along with additional rules that monitor allowlisted hosts.  These additional rules can detect abnormal behavior associated with the allowlisted hosts, such as logins at different times, root logins, and other suspicious activities.  This assumes that preventive controls are already in place for these hosts.

Always remember that there is no one-size-fits-all solution and no perfect security.  Our goal is to create controls that protect against most attacks and rely on **other layers** of security to complement them.  However, even with these measures in place, incidents can still occur, and it is crucial to be prepared to respond to them.


## Rules
Here are some examples of rules I implemented as **[Sigma rules](https://github.com/SigmaHQ/sigma)**.

### Network Scan

```yaml
title: Network scan
description: Detects network scanners.
author: José Lopes
references:
    - https://attack.mitre.org/techniques/T1595/
status: experimental

id: b73c16eb-f226-4076-b52a-b4d49e0fb17e
date: 2023/05/30
related:
    - id: 4601eaec-6b45-4052-ad32-2d96d26ce0d8
    - type: similar
tags:
    - attack.t1595
    - tlp.white
level: medium

logsource:
    category: firewall
detection:
    private_ip:
        - src_ip|re: '(^127\.)|(^192\.168\.)|(^10\.)|(^172\.1[6-9]\.)|(^172\.2[0-9]\.)|(^172\.3[0-1]\.)|(^::1$)|(^[fF][cCdD])'
        - dst_ip|re: '(^127\.)|(^192\.168\.)|(^10\.)|(^172\.1[6-9]\.)|(^172\.2[0-9]\.)|(^172\.3[0-1]\.)|(^::1$)|(^[fF][cCdD])'
    protocols:
        - TCP
        - UDP
        - ICMP
    timeframe: 1m
    condition: private_ip and protocols and count(dst_ip) by src_ip > 128

falsepositives:
    - Vulnerability scanners
    - Monitoring systems
```

This rule monitors internal network traffic (either source or destination) in three protocols commonly used to perform network scan (TCP, UDP, ICMP).  During one minute it'll evaluate connections and will trigger if a single source IP connects (or tries to) to more than 128 IP addresses.

### Port Scan

```yaml
title: Port scan
description: Detects port scanners.
author: José Lopes
references:
    - https://attack.mitre.org/techniques/T1595/
status: experimental

id: ac3fa3e7-bc97-47e7-aa57-abfe442f724d
date: 2023/05/30
related:
    - id: 4601eaec-6b45-4052-ad32-2d96d26ce0d8
    - type: similar
tags:
    - attack.t1595
    - tlp.white
level: medium

logsource:
    category: firewall
detection:
    private_ip:
        - src_ip|re: '(^127\.)|(^192\.168\.)|(^10\.)|(^172\.1[6-9]\.)|(^172\.2[0-9]\.)|(^172\.3[0-1]\.)|(^::1$)|(^[fF][cCdD])'
        - dst_ip|re: '(^127\.)|(^192\.168\.)|(^10\.)|(^172\.1[6-9]\.)|(^172\.2[0-9]\.)|(^172\.3[0-1]\.)|(^::1$)|(^[fF][cCdD])'
    port:
        - dst_port: '<1024'
    protocols:
        - TCP
        - UDP
    timeframe: 1m
    condition: private_ip and protocols and count(port) by dst_ip > 10

falsepositives:
    - Vulnerability scanners
    - Monitoring systems
```

This rule is very similar to the previous one, only monitoring internal traffic in some protocols (ICMP was removed because it has no associated ports).  Note that I'm monitoring only well-known ports (first 1024 ports), but this rule could be improved to monitor higher ports with a higher threshold.
