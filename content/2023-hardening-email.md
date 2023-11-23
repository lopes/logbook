+++
title = "Hardening Email with DKIM, SPF, DMARC"
date  = 2023-05-24
description = "Boost email security: Learn how DKIM, SPF, and DMARC can protect your domain from cyber threats."

[taxonomies]
tags = ["security"]
+++

Email is a communication tool whose history is intertwined with that of the internet itself.  Being an age-old network service, emails are susceptible to various threats, most of which are inherent to the stack of protocols that make up the solution.  Over the years, several means to mitigate such issues have been developed and in this article, I will focus on three specific methods directly related to DNS configurations, which are highly effective in bolstering the service and completely avoiding the most common threats: **DKIM**, **SPF**, and **DMARC**.


## DKIM
DomainKeys Identified Mail (DKIM, [RFC 6376](https://datatracker.ietf.org/doc/html/rfc6376)) is a security standard that safeguards email messages against tampering while in transit.  If you have ever examined an email header, you are aware that messages traverse multiple mail servers before reaching the final mailbox.  During this journey, there is a potential for a man-in-the-middle attack, where the message can be altered before reaching its intended destination.

DKIM utilizes public-key cryptography to sign the message, ensuring its integrity.  The DKIM configuration is done through a DNS TXT record with a specialized name based on the domain.  This record has a selector component that enables the email server to perform the necessary DKIM lookup in the DNS, usually in the format `selector._domainkey`.

The DKIM record in the DNS comprises various fields, including:

- `v=`: Version of DKIM in use
- `k=`: Key type
- `p=`: Public key content


## SPF
Sender Policy Framework (SPF, [RFC 7208](https://datatracker.ietf.org/doc/html/rfc7208)) is a specialized DNS record designed to identify authorized mail servers and domains that are allowed to send email on behalf of a domain. Similar to DKIM, SPF is set up using a TXT record. The name typically points to the root domain (`@`), and the content consists of key-value pairs:

- `v=`: SPF version
- `include:`: Points to a domain where the SPF records will be queried to determine whether the source IP is allowed or not
- `~all`: The tilde (`~`) indicates a soft fail action for all messages not defined in the include section

A `softfail` here can be considered a middle ground between failure and neutrality, which is useful when an organization is gradually adopting SPF.

{% admonition(type="warning", title="Warning") %}
It is not allowed to have multiple SPF records as the receiving mail server won't know which SPF rules to follow.
{% end %}


## DMARC
Domain-based Message Authentication, Reporting, and Conformance (DMARC, [RFC 7489](https://datatracker.ietf.org/doc/html/rfc7489)) is an email authentication and validation system that aids in protecting against spam, phishing, and spoofing. DMARC provides two types of reports:

- **RUA**: Reporting URIs for Aggregate Data --which consists of combined data in XML format without personally identifiable information
- **RUF**: Reporting URIs for Failure Data --which contains details of individual emails in plain text, including personally identifiable information

DMARC is configured through a DNS TXT record with the name `_dmarc`.  The content of this record comprises key-value pairs, such as:

- `v=`: DMARC version
- `p=`: The policy in use (can be `none`, `quarantine`, or `reject`)
- `rua=`: The email address where the RUA will be sent

By employing these three authentication methods together, organizations can effectively safeguard against various threats.  DKIM and SPF establish the legitimacy of the senders to the recipients, while DMARC instructs the recipients' servers on how to handle emails when DKIM or SPF checks fail.

While implementing DKIM, SPF, and DMARC is relatively straightforward, it is crucial to approach the configuration with caution to prevent disruption of user communication. It is advisable to start with more lenient policies initially and gradually transition to more secure configurations after successful testing. Here are some examples:

- **SPF**: You can begin with `softfail` or even `neutral` policy, and then move to `fail` once you are more confident.
- **DMARC**: Start with `none` policy, observe, and later change it to `quarantine` or `reject` as needed.

Keep in mind that DNS changes take time to propagate correctly (depending on the DNS's TTL), and improper changes can have serious consequences.  It is recommended to make these changes outside of business hours and conduct thorough testing before and after implementing any modifications.  Also, it is essential to maintain a record of every change as you may need to revert it due to unforeseen issues.

During my tenure as the leader of the SOC, we faced a situation where we stopped receiving emails from an important partner after activating SPF.  We handled that by temporarily setting the SPF policy to `neutral` until the partner resolved the issue on their end.  Although we encountered a few similar issues (users not receiving emails from partners due to SPF blocks), such occurrences became increasingly rare over time.  This improvement can be attributed to the growing adoption of email as a service (SaaS) and the implementation of better security practices.

As configuration changes vary for each service/server and detailed guides are readily accessible online, I won't delve into the specifics of the implementation process in this article.  The key takeaway is that all three methods effectively ensure email **integrity**, safeguarding senders and recipients from tampering, spoofing, and related attacks.  Enhancing **confidentiality** and **availability** requires implementing additional methods, but that's another story.


## References
- CloudFlare: [What are DMARC, DKIM, and SPF?](https://www.cloudflare.com/learning/email-security/dmarc-dkim-spf/)
- CloudFlare: [What is a DNS DKIM Record?](https://www.cloudflare.com/learning/dns/dns-records/dns-dkim-record/)
- Antispam.br: [SPF](https://antispam.br/admin/spf/)
- PowerDMARC: [What is SPF Include?](https://powerdmarc.com/what-is-spf-include/)
- Mimecast: [What is a DMARC Policy?](https://www.mimecast.com/content/dmarc-policy/)
