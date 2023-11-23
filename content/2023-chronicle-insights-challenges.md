+++
title = "Chronicle SIEM: Insights and Challenges Explored"
date  = 2023-09-23
description = "Exploring Chronicle SIEM: Features, benefits, and challenges in a review centered around Threat Detection."

[taxonomies]
tags = ["logging", "siem", "security"]

[extra]
image = "images/logos/google-chronicle.png"
+++

I've been using Chronicle SIEM for approximately six months now, and while reviewing my notes from this period, I've decided to compile everything into a blog post.  In this post, I aim to share my personal highlights of the tool and provide insights into areas where there's room for improvement.  Since there's limited information available about this relatively new system on the internet, I hope this post can be of help to others embarking on their own Chronicle SIEM journey.

{% admonition(type="note", title="Note") %}
It's worth noting that my overall perception is that Chronicle SIEM is a system in active development.  Over the course of these six months, I've witnessed features being added overnight and subtle changes in the User Interface.  As a result, some of the insights I share here may evolve as the system matures.
{% end %}

![Chronicle SIEM logo](/images/logos/google-chronicle.png "Chronicle SIEM logo: A stylized C followed by the word Chronicle.")


## Unified Data Model (UDM)

The [Unified Data Model (UDM)](https://cloud.google.com/chronicle/docs/reference/udm-field-list) stands out as one of the most impressive features of Chronicle SIEM.  Essentially, UDM is a comprehensive schema designed to store data from various types of logs in specific fields.  Some of these fields directly capture data from the logs, such as `principal.user.userid` for storing usernames, while others map data from the logs to predefined values, such as `security_result.action` to represent system decisions like `block` or `allow`.

This concept is particularly appealing to me because UDM addresses the challenge of parsing, normalizing, and indexing data in a consistent manner.

### Parsing
After ingestion by SIEM, almost all logs undergo parsing.  This crucial step ensures that valuable data from the logs is processed for further, simplified use.  [Logstash](https://www.elastic.co/guide/en/logstash/current/introduction.html) syntax is employed for developing the parsers, which is advantageous given the abundance of information available online about Logstash, making it accessible to many engineers.

Additionally, the Logstash subsystem not only allows data retrieval but also data transformation before outputting, a key capability for the subsequent normalization step.

### Normalization
As mentioned earlier, Chronicle has the ability to modify data found in raw logs (without altering the raw log itself).  This step proves invaluable for analysts querying data in SIEM or crafting detection rules.  Consider firewall logs, for instance: Some firewalls may log blocks as `DROP`, `Drop`, `FALSE`, or `block`. If this data were parsed as is, using it later would require cumbersome lines of code like this:

```yaml
(
  $e.security_result.action = "drop" nocase or
  $e.security_result.action = "false" nocase or
  $e.security_result.action = "block" nocase
)
```

As a programmer, I find this approach inelegant.  If you encounter a new firewall that logs the same action as `0`, you'd have to add a new line to accommodate it.  Regular expressions could be an alternative, but they too can be cumbersome and impact performance unnecessarily:

```yaml
$e.security_result.action = /(drop|false|block|0)/ nocase
```

Clearly, some log fields can be normalized to simplify data utilization, and that's precisely the purpose of this normalization step.

### Indexing
Once important fields have been extracted from the logs and properly normalized, they need to be stored for future use.  This is where UDM comes into play; its schema maps the most suitable properties for storing processed log data.  The schema follows a hierarchy and specific rules, though due to its vastness, it's beyond the scope of this text to cover all its intricacies.  However, to provide an example:

- `metadata`: Contains data not typically found in the log itself but valuable for providing additional context.  Examples include:
    - `metadata.log_type`: A string indicating the log type.
    - `metadata.product_event_type`: A concise, human-readable event name or type specific to the product.
    - `metadata.vendor_name`: The name of the product vendor.
- `principal`: Represents the entity responsible for the activity described in the event.  Examples include:
    - `principal.ip`: A list of IP addresses associated with a network connection.
    - `principal.user.*`: User information.
    - `principal.hostname`: Client hostname or domain name, which can also serve as the domain for remote entities.
- `target`: Represents the entity being referenced by the event or an object on the target entity.  Examples include:
    - `target.ip_geo_artifact`: Enriched geographic information related to an IP address, including location and network data.
    - `target.port`: Source or destination network port number in an event describing a specific network connection.
    - `target.process`: Process information.
- `security_result`: Contains security-related metadata for the event.  Examples include:
    - `security_result.description`: A human-readable description.
    - `security_result.attack_details`: MITRE ATT&CK details.
    - `security_result.action`: Actions taken for this event.
- `network`: This is where all network details are stored, including sub-messages with details on each protocol (e.g., DHCP, DNS, HTTP, etc.).  Examples include:
    - `network.asn`: Autonomous system number.
    - `network.session_id`: Network session ID.
    - `network.http.*`: HTTP information.

{% admonition(type="note", title="Note") %}
UDM fields represent raw log data.  Therefore, not all fields from the raw log will necessarily be present in the UDM representation.  However, some additional fields may be present, sourced from enrichment subsystems, to provide better context for events.
{% end %}

All these steps take place within the parser configuration, except for enrichments, which are added later.  After this process, Chronicle SIEM retains the untouched raw log (important for compliance) and the associated UDM (crucial for searching and detection).


## YARA-L

For me, [YARA-L](https://cloud.google.com/chronicle/docs/detection/yara-l-2-0-syntax) is the perfect complement to UDM.  YARA-L is akin to a fork from YARA, and if, in the words of Victor Alvarez (the creator of YARA), *"YARA is to files what Snort is to network traffic,"* then I define YARA-L as *"YARA-L is to UDM what YARA is to files."*

YARA-L closely resembles YARA but exhibits unique characteristics.  Let's examine an example rule I found on the internet:

```yaml
rule port_scan_from_single_ip {
  meta:
    author = "Google Cloud Security"
    description = "Detection of a single IP attempting to connect on 50 unique TCP/UDP ports over a 10-minute window."
    severity = "Medium"
  
  events:
    $event.metadata.event_type = "NETWORK_CONNECTION"
    $event.target.ip = $ip
    $event.target.port = $port
    
  match:
    $ip over 10m
    
  condition:
    #port >= 50
}
```

This rule comprises well-defined sections:

- `rule`: Where the rule name and its boundaries (delimited by curly brackets) are defined.
- `meta`: A section used for documentation purposes.
- `events`: Conditions for filtering events and specifying the relationships between events.
- `match`: Values to return when matches are found.
- `condition`: A condition to check events and the variables used to find matches.

{% admonition(type="info", title="Info") %}
I don't prefer placing a placeholder on the right side of an assertion (L9-L10), but you can invert it with the same results.
{% end %}

In this particular example, the rule tracks network connection logs, monitoring each unique destination IP and destination port for 10 minutes.  If more than 50 ports are accessed by a single IP address, the rule triggers.

{% admonition(type="note", title="Note") %}
This rule is provided as an example to illustrate the structure of YARA-L, and I'm not evaluating its effectiveness here.
{% end %}

While these are the most important sections of YARA-L, the language encompasses other sections.  Here's the complete structure:

```yaml
rule rule_name {
  meta:
    // Stores arbitrary key-value pairs of rule details, such as:
    // author, description, and severity.

  events:
    // Conditions to filter events and the relationship between events.

  match:  // optional, except for multi-event rules
    // Values to return when matches are found.

  outcome:  // optional
    // Additional information extracted from each detection.

  condition:
    // Condition to check events and the variables used to find matches.

  options:  // optional
    // Options to enable or disable while executing this rule.
}
```

By leveraging UDM, YARA-L abstracts much of the complexity of working directly with raw logs.  This results in simpler yet powerful rules that are easy to write, read, and, consequently, maintain.  Moreover, the ability to insert single-line (`// comment`) and multi-line (`/* comment */`) comments makes it even easier to document the original intent of the rule.

In my view, YARA-L excels where [Sigma rules](https://graylog.org/post/the-ultimate-guide-to-sigma-rules/) fall short.  When writing a Sigma rule, you often encounter ambiguity due to the format's limitations.  This can lead to misinterpretations and mismatches.  Perhaps this is why most available Sigma rules describe simple events.  When it comes to more specific patterns, Sigma lacks the mechanisms to accurately represent them.

In contrast, YARA-L offers features for representing multi-events, sliding windows, and boolean logic with operators such as AND, OR, ALL, and ANY.  When it comes to writing rules that can be adapted to various environments, YARA-L allows us to create rules with only a few UDM fields, effectively abstracting the log source within a rule, if needed.

### Reference Lists
[Reference lists](https://cloud.google.com/chronicle/docs/reference/reference-lists), as the name suggests, are structures where you can insert datasets that share certain attributes, such as lists of usernames, IP addresses, or even patterns.  Chronicle enables the creation of three types of reference lists:

- **String lists**: Composed of case-sensitive strings, these lists can be up to 6 MB in size, with a maximum line length of 512 characters.
- **CIDR lists**: These contain IP addresses written in CIDR notation to denote single IP addresses or entire subnets.
- **Regex lists**: Comprising patterns described in regex, these lists help identify specific content.

By utilizing reference lists, your YARA-L rules can become even more concise and readable.  For example, to prevent raising alerts for vulnerability scanners, you could create a reference list named "vulnerability_scanners." Then, in your detection rule, you'd simply add the following line to exclude detections:

```yaml
not $e.principal.ip in %vulnerability_scanners
```

This approach is elegant, straightforward, readable, and reusable.  These lists can be reused across multiple rules, facilitating the maintenance of rules.  If you automate the process of updating these rules through Chronicle's API, it becomes even more efficient.

## Searches
The syntax for searches closely resembles that of rules.  In fact, writing a rule is much like crafting a search that is triggered periodically, marking the results as detections.  You can also incorporate reference lists into your searches to fine-tune result filtering.  When combined with saved searches, this provides ample room for threat hunters to automate their work within SIEM.


## Pain Points

While Chronicle is an impressive tool, it's not without its challenges.  As mentioned earlier, Chronicle appears to be a software in active development.  Unlike mature platforms like Splunk, Chronicle lacks some basic features that I found myself missing:

- **Deleting Unneeded Rules**: While you can archive rules, there's no built-in functionality to delete them.  This can be frustrating, especially for those who like to keep their environment organized.  Sometimes, you create a rule for testing purposes, and later, you want to delete it, but this isn't straightforward.  The only available method for deleting rules is through the API.

- **Deleting Unneeded Reference Lists**: This issue is similar to the previous one, but it's exacerbated because there's no way to archive or delete reference lists via the API either.  The only option is to ignore them.  To make matters worse, after creating a rule, you're not allowed to rename it.

- **Parsing Problems**: UDM relies on parsers, and their accuracy is crucial.  However, some "Golden Parsers" (parsers developed by Google) have been known to lose important information or exhibit unusual bugs.  I've encountered instances where parsers discarded valuable data or linked numerous raw logs to a single UDM event.  While Google promptly addressed these issues upon reporting, it does raise concerns about testing procedures.

- **Version Control Lacking Basic Information**: The rules editor, serving as an integrated development environment (IDE), provides version control to track changes during the rule's lifecycle.  However, this version control lacks a fundamental feature: It doesn't display the user who made the change.  Even after requesting this feature from Google, I received a resounding "no."  The absence of such a basic feature necessitates workarounds like using variables in the `meta` section to track the rule's creator or creating parallel controls.

- **Editing Rules in Bulk**: The user interface does not offer features to edit rules in bulk or enable/disable a set of rules simultaneously.  It's nearly impossible to change a variable name across multiple rules at once through the UI.  Fortunately, it seems possible to perform bulk edits through the API.

- **Limited Built-in Roles**: Chronicle ships with just four roles, which may not be sufficient for larger teams.  Google has suggested the possibility of using Google Cloud Identity as the Identity Provider (IdP) to create custom roles, referred to as "Bring Your Own Identity" (BYOID).  While I haven't explored this option, it appears more like a GCP feature integrated into Chronicle rather than an intrinsic feature of Chronicle itself.


## Takeaways

Working with Chronicle SIEM has been a rewarding experience.  This tool seems to be built on a solid foundation, and I anticipate it gaining popularity in the years to come.  Unfortunately, it's still a relatively new solution, and Google is in the process of refining the environment, particularly the parsers.

As the entire ecosystem matures, Chronicle SIEM has the potential to become one of the premier SIEM solutions.  However, achieving this status will require time and attentive listening to user feedback.  Additionally, when it comes to Incident Response, the potential use of [Google Colab](https://colab.research.google.com/) to create notebooks for automating investigative steps looks promising.

There's still much for me to explore within Chronicle, such as parsers, APIs, and enrichments.  Perhaps in the future, I can delve deeper into these topics.  Long live Chronicle SIEM!


## References

I'm leaving some references so you can learn more about Chronicle SIEM.

- [Chronicle SIEM Fundamentals](https://learn.chronicle.security/courses/chronicle-siem-fundamentals)
- [UDM Schema](https://cloud.google.com/chronicle/docs/reference/udm-field-list)
- [YARA-L](https://cloud.google.com/chronicle/docs/detection/yara-l-2-0-syntax)
- [YARA-L: A New Detection Language for Modern Threats](https://go.chronicle.security/hubfs/YARA-L%20Overview%20White%20Paper.pdf)
- [Chronicle Road to Detection](https://medium.com/anton-on-security/chronicle-road-to-detection-yara-l-language-part-3-of-3-5f102ff22934)
- [@thatsiemguy](https://medium.com/@thatsiemguy)
