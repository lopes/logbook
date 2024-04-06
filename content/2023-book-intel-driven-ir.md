+++
title = "Intelligence-Driven Incident Response"
date  = 2023-10-17
description = "Discover how integrating CTI enhances Threat Detection and CSIRT."

[taxonomies]
tags = ["review", "security", "threatdetection"]

[extra]
cover_image = "images/pic-crow.jpg"
+++

As I transitioned from Incident Response to Threat Detection, I felt the need to better understand how I could deliver more value to our CSIRT and address some of the problems I encountered while handling incidents.  That's when a friend recommended the [Intelligence-Driven Incident Response, 2nd Edition](https://learning.oreilly.com/library/view/intelligence-driven-incident-response/9781098120672/) (IDIR) book as a resource that would systematically present Cyber Threat Intelligence (CTI) terminology and illustrate how each piece of the puzzle fits together.  Motivated by this, I began reading this (spoiler) excellent book, and here I'm sharing my impressions after finishing it.


## The Book

In the context of this book, IDIR is a methodology that integrates CTI and Incident Response (IR).  To achieve this, the book introduces the F3EAD Cycle, a military approach consisting of 6 phases:

- **Find:** Identifies relevant adversaries for IR.
- **Fix:** Puts intelligence gathered in the previous phase to work in tracking down signs of adversary activity (Threat Detection).
- **Finish:** Eradicates the footholds that malicious actors place in the network and remediates whatever enabled them to gain access in the first place.
- **Exploit:** Extracts information that has been collected during pre-operations.
- **Analyze:** Transforms data and information into intelligence.
- **Disseminate:** Involves organizing, publishing, and sharing developed intelligence.

Most of the book is dedicated to a deep dive into each of these phases, offering examples and numerous references to provide context for the reader.  The authors also introduce relevant concepts to help put the cycle into action.  For example, to facilitate better analysis (the “A” in F3EAD), they present concepts such as (a) fast/slow thinking, (b) deductive/inductive/abductive reasoning, and (c) various types of biases.


## Opinions

In my opinion, this is an absolutely awesome book because it taught me how the many concepts of CTI fit together, like the Pyramid of Pain, Cyber Kill-chain, MITRE ATT&CK, and so on.  Most importantly, I learned how CTI, Threat Detection, and CSIRT can better integrate to deliver improved results.

I found some sections challenging to read, either because the authors presented too much non-technical content in a condensed manner or included too many examples.  As a highly-technical professional, I sometimes found it tedious to read non-technical content, such as the strategies for analyzing and disseminating intelligence.  However, even with that feeling, I recognize that many concepts presented there are vital, and as I'm not a CTI professional, it's acceptable for me to have a more superficial understanding of those parts.

{% admonition(type="note", title="Note") %}
Despite some phases of the F3EAD cycle being focused in CTI, I found them applicable to IR.  For example: Improving your analysis skills can help you on incident investigations and learning better strategies to disseminate content can be helpful on sharing IR reports.
{% end %}

This book is a must-read for Information Security professionals in Security Operations roles.  It can significantly enhance your ability to collaborate with other teams by facilitating communication in a common language and improving your understanding of what to expect from other teams.  It also offers valuable insights on how to enhance the deliverables from your own team, enabling more people to benefit from your work.  As a Threat Detection professional, I now have a clearer idea of what to expect from the CTI team, and having their concepts in mind makes it easier to interpret their contributions to my team and create detection rules aligned with their research.

In conclusion, this book is not an endpoint; it's a fantastic starting point for delving deeper into some of the concepts presented.  Once you grasp the bigger picture, you can more effectively choose the topics to focus your research on.  It's like embarking on a shopping spree for Security Operations professionals.  **Highly recommended**.
