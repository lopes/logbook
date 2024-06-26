+++
title = "Integrating MISP with Chronicle SIEM"
date = 2024-06-26
description = "Streamline the integration between CTI and CSIRT in an automated and efficient way."

[taxonomies]
tags = ["automation", "csirt", "logging", "security", "siem", "threatdetection"]

[extra]
mermaid = true
cover_image = "images/logos/misp.png"
+++


The [Malware Information Sharing Platform (MISP)](https://www.misp-project.org/) has been on my radar since 2019 when I first encountered it at [CERT.br's Fórum Brasileiro de CSIRT](https://forum.cert.br/forum2019/agenda/).  Since then, I've been looking for opportunities to use it in Security Operations (SecOps), without success.  Until now.  Recently, while working as a Threat Detection Engineer, I was involved in the MISP integration with Chronicle SIEM.  I seized this opportunity to learn more about the process.  In this post, I'll share my experience with concepts, examples, and tips, covering not only the "hows" but also the "whys" and practical implications.

Let's get started!


## Definitions and Architecture
MISP is a Cyber Threat Intelligence (CTI) tool used to manage events (aka Indicators of Compromise -- IOCs) for further distribution (sharing).  By definition, IOCs shouldn't be treated as ordinary logs in SIEMs because they don't represent actions taken in the environment.  Instead, they carry data that can be used to **contextualize** such events.  In Chronicle, while ordinary logs are stored in the Unified Data Model (UDM) format, supporting data can be stored either in Reference Lists (RL) or in Entity Graph (EG) formats for further use in allow lists or, in our case, to add context.

Given the ephemeral nature of IOCs, we cannot assume that a value will always be marked as malicious because it's more of a transitory state.  That said, if we choose to use RLs, we'll need to create additional middleware to automatically manage such lists, removing IOCs that are no longer malicious or relevant to our scope.  An easier way to handle this in Chronicle is by using EGs because, although they are treated like regular UDM data, they are managed differently, with one significant change being that Chronicle **expires** them periodically.

With this in mind, I set the goal of ingesting MISP events into Chronicle as EGs.  Later, we want to leverage these IOCs by correlating them with our logs (UDM) to detect any suspicious activities in the form of alerts.  I divide this process into three steps: Ingestion, Parsing, and Detection.  In the next sections, I'll delve into more details on each of these steps, but first, I'm sharing the big picture in the following sequence diagram.

{% mermaid() %}
```mermaid
  sequenceDiagram
  box Ingestion
    participant MISP as MISP
    participant S3 as S3
    participant CI as Chronicle<br>Ingestion
  end
  box Parsing
    participant CP as Chronicle<br>Parsing
    participant CN as Chronicle<br>Indexing
  end
  #box Detection
  #  participant CD as Chronicle<br>Detection
  #  participant CA as Chronicle<br>Alerting
  #end
  MISP->>S3: Exports events<br>(JSON)
  CI->>S3: Get new events
  activate S3
  S3-->>CI: Delivers the events
  deactivate S3
  activate CI
  CI->>CP: Raw logs
  deactivate CI
  activate CP
  CP->>CN: UDM/EG<br>(parsed + normalized)
  deactivate CP
  #CD->>CN: Search for suspicious<br>events
  #activate CN
  #CN-->>CD: Events found
  #deactivate CN
  #CD->>CA: New alert with<br>related events
```
{% end %}
{% mermaid() %}
```mermaid
  sequenceDiagram
  #box Ingestion
  #  participant MISP as MISP
  #  participant S3 as S3
  #  participant CI as Chronicle<br>Ingestion
  #end
  box Parsing
    participant CP as Chronicle<br>Parsing
    participant CN as Chronicle<br>Indexing
  end
  box Detection
    participant CD as Chronicle<br>Detection
    participant CA as Chronicle<br>Alerting
  end
  #MISP->>S3: Exports events<br>(JSON)
  #CI->>S3: Get new events
  #activate S3
  #S3-->>CI: Delivers the events
  #deactivate S3
  #activate CI
  #CI->>CP: Raw logs
  #deactivate CI
  activate CP
  CP->>CN: UDM/EG<br>(parsed + normalized)
  deactivate CP
  CD->>CN: Search for suspicious<br>events
  activate CN
  CN-->>CD: Events found
  deactivate CN
  CD->>CA: New alert with<br>related events
```
{% end %}


## Ingestion
Ingestion starts by exporting events from MISP and ends when they're available in Chronicle as raw logs.  As mentioned, there are plenty of ways to do this in MISP, but I designed an architecture where MISP exports events to an S3 bucket, and we use Chronicle's built-in feature to ingest data directly from there.  At this point, it's also crucial to define the format for these events. In our case, we started with CSV but soon switched to JSON to take advantage of other features in Chronicle in the next phases.  Fortunately, MISP's flexibility and this simple architecture made this transition smooth because, since we weren't implementing any fancy-complex RESTful API, there weren't many changes needed.

{% admonition(type="warning" title="Warning") %}
In this architecture, the S3 bucket must be properly protected because if it is compromised, there's a high risk of impacting all aspects of the CIA triad.  Remember: SIEM will blindly trust every information coming from MISP.
{% end %}

When you export data from MISP, the response will be a large JSON structure (or similar format) with all returned events nested.  Instead of just writing it down into a file, we made sure to save each event in a single file for two reasons:

1. Chronicle has a limit of 1 MB ingestion per file.
2. It will be much easier to write a parser.

Moving away from the technical details of ingestion, ensure your MISP shares enough **context** along with each IOC, such as the APT or campaign associated or the systems affected.  **Think like an incident responder**: If you get an alert with a single-dry IP address stating it's malicious, how do you proceed?  Now, if you get the same IP address with contextual information, like the malware family it's associated with, the APT that uses it, the Techniques (MITRE ATT&CK) it's linked to, notes from the CTI team, and possibly some reference URLs, you can decide the next move more easily and with less stress.  Clearly, this will also <mark>reduce the Mean Time to Triage/Mitigate</mark>, effectively **enhancing security**.

In Chronicle, I found the MISP Threat Intelligence (`MISP_IOC`) to be the best data label for the tuple (MISP, JSON).  Note that Chronicle has a built-in parser associated with this label, but it didn't work.  So, after getting the raw logs in Chronicle, I had to write my own parser.


## Parsing
Parsing in Chronicle means [Logstash](https://www.elastic.co/logstash), poor documentation, and a bad User Interface (UI).  Logstash is a popular open-source tool to parse and normalize logs, and it is the "L" in the famous [ELK Stack](https://www.elastic.co/elastic-stack/).  Despite its popularity, I couldn't find any concise documentation on it, like a "Logstash 101" book.  Don't get me wrong: there's a community with forums and guides, but I missed something more didactic.  I got very confused in many aspects while using Logstash, from the raw log entry to the output.  Also, I couldn't find any official documentation from Google on it. 😕  It felt like they were saying: *"We use Logstash, figure it out yourself."* ✌🏻

For those like me who are new to Logstash, it's a pipeline tool that can ingest data from multiple sources, transform it, and send it to multiple destinations.  This way, a parser structure will follow the pattern below (considering the use in Chronicle):

```bash
filter {
  # initialize variables
  mutate {
    replace => {
      "var_1" => "value_1"
      "var_2" => "value_2"
    }
  }

  # pre-process the raw log with Logstash's built-in filter
  # (taking advantage of the JSON format)
  json {
    source => "message"
    array_function => "split_columns"
    on_error => "_not_json"
  }

  # error checking: valid JSON?
  if [_not_json] {
    drop { tag => "TAG_MALFORMED_MESSAGE" }
  }
  else {
    # your custom parsing logic here with normalization routines
    # **usually**:
    #   1. pre-process data using local variables
    #   2. assign variables to UDM fields using an "anchor", like "event"
    #     - UDM prefix: event.idm.read_only_udm.
    #     - EG prefix.: event.idm.entity.

    # write the event before finish
    mutate {
      merge => { "@output" => "event" }
    }
  }
}
```

Building a parser and taming Logstash is just the first step because, ultimately, parsing in Chronicle means taking any piece of data from the raw log and attributing it to a UDM field.  The [UDM schema](https://cloud.google.com/chronicle/docs/reference/udm-field-list) is colossal, and its documentation is just a list of fields with their types and some notes.  As I advanced in my quest, when testing the parser, I encountered some strange error messages (I'll discuss this later).  After many trial-and-error loops, unsuccessful Googling, 👀 and some stressful interactions with Google Support, I figured out that I was leaving some required fields blank in the resulting EG.  Yes, UDM and EG have some **required fields** depending on many scenarios, and it's documented nowhere. 🙄

As I made progress with the parser, wanted to test it against some samples to ensure I was on the right track.  Although Chronicle provides a UI for writing and testing this parser, it's far from good.  Eventually, Chronicle will redo the SSO handshake, refresh the screen, and make you lose your work.  When this happened to me, I started copying and pasting my work locally to avoid further losses, which is... duh!  It's strange because writing a parser demands time, and it's normal to spend days or weeks on it (IMO).  Moreover, the **error messages** are often enigmatic and sometimes misleading.  On one occasion, I was getting a clear error message stating that the field `metadata.entity_type` was missing, but it was there, in a code block that was always executed.  Hours later, I figured out that I was missing one threat field, and once I filled it, everything worked magically. 😵

{% admonition(type="tip" title="Pro Tip") %}
Put the `statedump{ label => "pre-output merge" }` line right before the last `mutate` block in your parser to see the parsed event before it's sent to the output.  This way, you can debug the parser more easily.  Remove it when you're done.
{% end %}

After the tests, you have to validate your parser before enabling it in production, and here I found another annoying problem. Validating a parser means making Chronicle test it against some logs. If an error is found, the validation ends with an error, and you must implement or adjust any routine to better interpret certain part of the raw log. The problem is that we started with CSV a couple of days before switching to JSON, and the validation tool doesn't allow you to change the time range for the tests. This meant that I had to implement routines that are useless for JSON.

The bottom line is that I managed to write the parser in a rough and tumble way (you can find a version of it [here](https://gist.github.com/lopes/51482ce584414e1dde3d0b67bbe0c501)).  I learned a lot about Logstash during this effort and got some gray hair thanks to Google, but that was only halfway there.  Now, I needed to use the data to implement some analytics.

{% admonition(type="tip" title="Pro Tip") %}
Writing a parser is far from easy, so defining a standard format (like JSON) that you and your team will use across your data sources can save time in the future, as you'll be able to reuse some code snippets.
{% end %}

{% admonition(type="warning" title="Warning") %}
Keep in mind that to make the IOCs expire, Chronicle requires the `metadata.interval.end_time` field to be filled.  If it's not, they will be marked as Dec 31, 9999, and will expire with your ordinary logs.  Also, despite not being required, the `metadata.interval.start_time` must be filled to make the analytics work.

In my experience, it seems that Chronicle considers the IOCs only when the timestamps of the logs correlated with the them are within the `start_time` and `end_time` range.  This makes sense because if I add a new IOC tagged as malicious starting on Jun 20 and perform a retro hunt with this rule starting on Jun 17, I don't want it to trigger before Jun 20 because the item wasn't malicious yet (in theory).
{% end %}

{% admonition(type="bug" title="Heads up") %}
Hey Google!  A better documentation, a reviewed UI, a public repository, and a trained AI (you're the Gemini owner, right?) might solve [almost] all these issues! 💡
{% end %}


## Detection
Writing a parser is hard, but since you've written it, you understand where each piece of data is and how to use it.  At this point, I had to leave MISP and look at the available data sources because we needed to correlate events.  I approached this by grouping the IOC types into two categories, Network and Endpoint, because it would help me find the best log sources or log types to match these events.

After some research, I noted that using the `metadata.event_type = "NETWORK_CONNECTION"` would let me cross-reference the IP addresses listed as IOCs with more than just the firewall logs.  Similarly, when dealing with MISP, I want to use an Entity Type that matches that IOC type, like `graph.metadata.entity_type = "IP_ADDRESS"`.  This initial filtering is important to make the rule faster by avoiding out-of-scope events.  As a rule of thumb, a YARA-L rule of this kind will look like this:

```c
rule misp_ioc_ip_outbound {
  meta:
    author: "Joe Lopes"
    description: "MISP IOC rule: outbound IP address"
    date: "2024-06-20"

  events:
    // 1. basic filters for ordinary events ($e)
    $e.metadata.event_type = "NETWORK_CONNECTION"
    not $e.target.ip in cidr %private_networks

    // 2. lock the field in the events with a placeholder
    $ip = $e.target.ip

    // 3. basic filter over entity graph to match MISP ($misp)
    $misp.graph.metadata.entity_type = "IP_ADDRESS"
    $misp.graph.metadata.vendor_name = "Circl"
    $misp.graph.metadata.product_name = "MISP"

    // 4. lock the IOC value using the same placeholder
    $ip = $misp.graph.entity.ip

  match:
    $e over 10m

  conditions:
    $e and $misp
}
```

This approach will require you to create one rule per IOC type, but since the types are limited, you'll end up with just a few new rules.

{% admonition(type="tip", title="Pro Tip") %}
Eventually, the SIEM will not be the best place for some rules.  For example, if your SIEM only gets alerts from the EDR, you'll want to implement this integration there.
{% end %}

When the rules are in place, it's time to test them in the battlefield. 🪖  If you have any automated tools like [Atomic Red Team](https://atomicredteam.io/), [Caldera](https://caldera.mitre.org/), or [Cymulate](https://cymulate.com/), you can make use of them.  Just make sure to use any IOC that's recorded in MISP and already ingested and parsed by the SIEM.  If you don't have such fancy tools, be creative. Add any fake events to MISP using any obscure IP address or a hash from an exclusive file on your machine.  Now, here's a good advice: When it comes to testing in Chronicle (or any other SIEM), keep in mind that real-time is "real-time," so expect some latency between the action and the trigger (you should be aware of it, but it's always nice to remember 😉).

{% admonition(type="info", title="Info") %}
Some tools, like certain flavors of Next-Generation Firewalls, might be able to capture hashes of files traversing them.  Additionally, some antivirus programs might have Host Intrusion Detection System submodules that allow them to monitor network data at the endpoint level.  This will mix up the IOC grouping and complicate your evaluation, although it's for the greater good.

Bottom line: Being aware of your tools' capabilities is challenging, mainly because the security team usually doesn't own such tools, but it is definitely a blessing and will help you writing better analytics.
{% end %}

{% admonition(type="warning", title="Warning") %}
This is just a simplified view of the process. Ideally, after getting the logs parsed into Chronicle, you'll follow your formal process for new analytics, including all tests and documentation.
{% end %}


## Final Thoughts
IOCs are at the base of the Pyramid of Pain, meaning it's quite easy for adversaries to bypass rules based on them.  On the other hand, it's also easy for defenders to create such rules.  When followed by an automated pipeline like the one presented here, your alerts will stay up to date with what your CTI team is monitoring, likely achieving the so-called **actionable intelligence**.

Try to take small and consistent steps if you venture into this journey.  Before moving to the next phase, assess your results and reevaluate your goals.  Have in mind that it's much easier to change something like the event format in the ingestion phase than in the detection phase because you'll have to redo things like, probably, a whole parser. 😱

Chronicle can be a real pain until the logs are parsed, normalized, and indexed.  But after that, it shows its versatility, allowing you to search and write analytics in an elegant and easy way because you're using a standardized naming system (UDM/EG).  Not only that, but Chronicle lets you do it completely independent of log sources which elevates the term **correlation** to another level.  The good news is that I'm aware Google is rolling out a new feature called "Dynamic Parser" which, as far as I know, will automatically parse JSON logs. ⏰

Another downside here is that such alerts may generate lots of false positives, but you can overcome this with curated lists and expiring IOCs (EG in Chronicle).  You can also choose to use these rules only against the most critical parts of your environment (users and assets).  While the latter idea won't reduce the percentage of false positives, it will certainly reduce the number of alerts, thus reducing alert fatigue in your SecOps.  An additional idea is to automate part of the triage of such alerts, like requesting users to confirm the alert, enforce a new MFA confirmation, or even blocking the connection for a while.  This is a more advanced topic, but it's worth mentioning.

In the end, the main goal is to have a more proactive approach to security, and this is just one of the many ways to achieve it.  I hope this article has helped you to understand how to integrate MISP with Chronicle SIEM and how to create rules based on IOCs! 👊🏻


## References
- [How to Integrate MISP and Chronicle SIEM](https://medium.com/@thatsiemguy/how-to-integrate-misp-and-chronicle-siem-9e5fe5fde97c)
- [Expiring IOCs in Entity Graph](https://medium.com/@thatsiemguy/expiring-iocs-in-entity-graph-373010554091)
- [UDM Event Validation](https://medium.com/@thatsiemguy/udm-event-validation-fa0d6f8bd1d4)
- [Threat Intelligence with MISP Part 7](https://kravensecurity.com/threat-intelligence-with-misp-part-7-exporting-iocs/)
- [Parser syntax reference](https://cloud.google.com/chronicle/docs/reference/parser-syntax)
- [Book: Practical Threat Detection Engineering](https://www.packtpub.com/en-us/product/practical-threat-detection-engineering-9781801076715)
