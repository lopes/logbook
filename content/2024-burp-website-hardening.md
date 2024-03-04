+++
title = "A Little Hardening with Burp"
date  = 2024-02-23
description = "Guide to MITRE ATT&CK with history and context for better usage."

[taxonomies]
tags = ["security", "netlify", "cloudflare"]
+++

Recently, I delved into [Burp](https://portswigger.net/burp), experimenting with some Professional Suite features, when it occurred to me that I had never tested my own website.  Despite having security in mind during the design phase (security by design), incorporating a static website and a Web Application Firewall (WAF), I recognized the importance of proactive testing.  In this post, I'll share the vulnerabilities I identified and the corresponding fixes.


## Running Burp
Not being a Burp expert, I won't turn this into a tutorial.  If you're interested, [this TryHackMe room](https://tryhackme.com/room/burpsuitebasics) offers valuable insights.  For my test, I included only my domain in scope, applied a filter to intercept only in-scope traffic, turned on the proxy, and navigated through the site.

After a few minutes, I turned off the proxy and analyzed the data within the Target menu.  The Enterprise Edition's vulnerability scan revealed issues I had overlooked during the initial setup.  In the following sections, I'll detail these findings.


## Strict Transport Security not Enforced
HTTP Strict Transport Security (HSTS) is a crucial feature that prevents users from connecting to the website over unencrypted connections — [learn more here](https://www.cyberis.com/article/five-minute-fix-http-strict-transport-security-hsts-not-enforced).  As [previously explained here](@/2023-cloudflare-get-started.md), this website's current architecture employs CloudFlare as a WAF, configured to enforce secure connections.  However, for usability reasons, I initially allowed insecure connections without enabling the HSTS feature, fearing it might deter potential visitors.

Things have changed.  I not only enabled HSTS but also set the minimum TLS version to 1.2.  Given that a significant aspect of this site revolves around Information Security, there's no better way to teach than by example.  If you try accessing [lopes.id](http://lopes.id/) through a deprecated browser using insecure TLS versions, you won't be able to open it.


## Frameable Response and Cacheable HTTPS Response
Frameable Response poses a clickjacking threat: A malicious actor could exploit my site to lure victims and compromise their systems, as [explained here](https://portswigger.net/burp/documentation/desktop/testing-workflow/testing-for-clickjacking).  I addressed this issue by configuring my web server, utilizing Netlify's configuration file.  [This Netlify's help page](https://docs.netlify.com/routing/headers/) provides guidance, and I implemented an additional mitigation against XSS attacks as suggested in the how-to.

The scan highlighted the importance of implementing cache control browser-side, which I addressed by referring to [this resource](https://www.imperva.com/learn/performance/cache-control/).  The final section in the configuration file was:

```toml
[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-XSS-Protection = "1; mode=block"
    Cache-Control = "no-store"
```


## Takeaways
That sums it up — only low-severity vulnerabilities found, all fixed in a matter of minutes.  This showcases the effectiveness of security by design and simpler architectures.  While it may not be applicable to all projects, remember that KISS principles apply to security too, and rejecting unnecessary features can enhance your project's security just by reducing the attack surface.

Regardless of the time invested in planning and deploying your project carefully, Information Security must be approached in a layered manner, and security tests fit into [almost] all scenarios.  Testing serves as a testament to security being a process, not a static state.  Periodic execution of tests is crucial, considering the continuous discovery of new vulnerabilities and the evolving nature of projects — ideally, new tests with each new feature, and preferably in a DevSecOps-automated way.
