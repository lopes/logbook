+++
title = "The Cuckoo's Egg"
date  = 2024-03-13
description = "A 1980s Infosec thriller with groundbreaking investigations."

[taxonomies]
tags = ["security", "review"]

[extra]
cover_image = "images/pic-cliff-stoll.jpg"
+++


While working in Infosec for many years, I only recently discovered ["The Cuckoo's Egg" by Cliff Stoll, published in 1989](https://www.kleinbottle.com/ExtrasAndMisc.htm).  I feel a bit ashamed for not knowing about it earlier, considering its fame.  Nonetheless, as they say, better late than never.  In the following lines, I'll share my impressions after reading this (spoiler) amazing book.

![Cliff Stoll](/images/pic-cliff-stoll.jpg "Cliff Stoll among computers")


## Plot and Context
The story unfolds in Berkeley, CA, during 1986-87, specifically in the astronomy laboratory.  There, the author, Clifford Stoll, known simply as Cliff, worked as a Systems Administrator for the Unix machines.  Despite his background in astronomy, Cliff landed this job, and on his first days, he stumbled upon a discrepancy of 75 cents in the accounting system.  This seemingly minor issue sparked his investigation, leading him to uncover that someone was exploiting the operating system and using their servers to pivot to military computers.  As Cliff delves deeper into the investigation, he finds himself embroiled in an international espionage plot, tracking a hacker from Germany.

This book transcends the realm of Information Security (Infosec) as it offers a fascinating testimony of a pivotal era in Information Technology (IT).  During this time, we witnessed the transition from mainframes to personal computers, the absence of a *de-facto* standard for Operating Systems (OSes), and the gradual emergence of Unix (with Linux not yet in existence).  Ethernet was in its infancy, and other networking protocols such as X.25 held sway, particularly for long-distance communications (WAN).  In fact, the term "Internet" was about to be widespread as the Arpanet and Milnet were the common terms to refer to this geographically distributed network of interconnected computers.

Indeed, the state of Infosec during that era could be likened to that of an unborn baby.  The Data Encryption Standard (DES) served as the default cryptographic algorithm, with little else available at the time.  Networks operated largely on trust, as evidenced throughout the book, with many best practices that are now standard not yet established.  Password sharing was common, Unix's cryptographic implementation lacked salt, protocols lacked confidentiality and integrity controls, and default accounts remained enabled with default passwords -—a chaotic landscape when viewed in retrospect.

{% admonition(type="note", title="Note") %}
It's important to consider the context of the time.  Implementing cryptography and other security controls back then often carried a significant overhead that could render systems unusable with the common processing power available.  This constraint led to the development of secure protocols like SSL/TLS, DMARC/DKIM, IPSec, and SSH, which emerged later when technology had advanced to better accommodate such measures without compromising usability.
{% end %}


## Impressions
Cliff's writing style is so personable that you feel as though he's a friend sharing his experiences with you.  He skillfully weaves together the narrative of the investigation with glimpses into his personal life, allowing readers to empathize with him.  There are moments when I found myself imagining what it would be like to walk in his shoes -—sleeping at work for days on end, spending entire weekends tracking the hacker (my wife would kill me).

The author's boundless energy, insatiable curiosity, and eagerness to learn are truly contagious.  Despite the absence of modern security tools, Cliff demonstrates remarkable ingenuity and resourcefulness. He constructs a sniffer using a wiretapped computer and a printer, develops a firewall in C-lang, sets up a Security Information and Event Management (SIEM) system using Unix and C-lang, complete with a beeper alert.  Moreover, with his creativity and expertise, he devises a honeypot --a clever solution to keep the hacker connected, enabling authorities to trace their activities.  Cliff's innovative approach to tackling security challenges exemplifies his exceptional skills and dedication to the field.

His methodical approach to the work is another testament of effectiveness.  Cliff maintains a detailed logbook, diligently updating and revisiting it regularly.  In the end, this practice proves to be a crucial tool in catching the hacker.  Many of his key insights stem from later analysis of his notes, which also serve as valuable evidence against the hacker.  Cliff's commitment to keeping thorough records not only aids in solving the case but also highlights the importance of documentation in Infosec investigations.

Stoll's lack of formal training in security doesn't deter him from taking action to achieve his objectives.  He leverages his creativity and diverse skill set to his advantage.  Despite the lack of assistance from official agencies, he forges his own path toward his goals.  Indeed, he embodies the essence of a one-man army, seamlessly transitioning between roles such as Incident Responder, Threat Detection Engineer/Analyst, Cyber Threat Intelligence (CTI) Analyst, Programmer, Network Engineer, and Sysadmin, among others.  His multifaceted expertise and determination serve as a truly inspiring example of what one can accomplish through sheer dedication and ingenuity.


## Final Thoughts
Absolutely, "The Cuckoo's Egg" is not merely an Infosec tale; it's a narrative that encapsulates the essence of the computer revolution during the 1980s.  It provides readers with a glimpse into the transformative shift from the mainframe era to the microcomputer era.  We witness the nascent days of Unix, marked by a lack of standardization and a pioneering spirit among early adopters.  Networks were still in their analog infancy, and the high skilled individuals who navigated this landscape, whom Stoll affectionately refers to as "wizards" and "dinosaurs," were true hackers in every sense of the word.  These were the individuals who wrote C programs to implement firewalls and ingeniously repurposed printers as sniffers.

Indeed, "The Cuckoo's Egg" transcends the boundaries of Infosec and computers; it morphs into a captivating espionage thriller interwoven with elements of romance and comedy.  For enthusiasts of IT or Infosec, it stands as an indispensable read, offering insights into the evolution of technology alongside a gripping narrative.  However, even for those not deeply entrenched in these fields, the book comes highly recommended for its ability to engage and entertain readers with its rich tapestry of intrigue and humor.  Whether you're a tech aficionado or simply someone seeking an fascinating story, "The Cuckoo's Egg" promises a memorable and rewarding reading experience.  It's surprising indeed that Hollywood hasn't yet brought this story to the big screen.

Cliff, you're a wizard.  Thank you for this.

> If I have seen further it is by standing on the shoulders of Giants.
— Isaac Newton
