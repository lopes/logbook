+++
title = "Getting Started with CloudFlare for Web Protection"
date  = 2023-01-02
description = "Learn how to use CloudFlare's free plan on personal sites for better security and protection."

[taxonomies]
tags = ["cloud", "cloudflare", "netlify"]

[extra]
image = "images/diagram-cloudflare-netlify.png"
+++

[CloudFlare](https://www.cloudflare.com/) is a great service for web protection and with its free plan, we're able to use many great features to improve the security of personal websites.  In the following lines I'll describe how I got it working for me.

{% admonition(type="note", title="Note") %}
Remember that my site is built upon Zola, deployed using Git/GitHub, and hosted in Netlify. [[1](@/2020-zola-sites-estaticos.md)][[2](@/2022-zola-change-theme.md)]
{% end %}


## Backing up Configurations

Before changing anything, make sure to back up relevant data from [Netlify](https://www.netlify.com/) and from the [Domain Name Registrar](https://en.wikipedia.org/wiki/Domain_name_registrar), like current name servers, digital certificates, and anything related to it.  Since Netlify redirects traffic to Google Cloud Platform, it's useful to get the real IP addresses for your site to test it latter.  Although you can use `ping` for this purpose, [httping](https://www.vanheusden.com/httping/) is better because it works directly on the Application layer (HTTP).


## Setting up

The following figure shows the architecture we're going to implement.  The first step is to open CloudFlare's dashboard and add the site/domain to be configured.

![Web Architecture with CloudFlare and Netlify](/images/diagram-cloudflare-netlify.png "Sequence diagram showing how the architecture works")

After entering the domain, CloudFlare will automatically fetch and add the current DNS configurations with similar data retrieved by httping.  While that can work, I recommend deleting all data and add manually according to Netlify's DNS data: Open [Netlify's DNS dashboard](https://docs.netlify.com/domains-https/netlify-dns/?_ga=2.53786856.179018823.1672665095-1288696901.1672665095) and take note of the following data under DNS Records section:

- **Name**: It's usually the domain you use, like `lopes.id`
- **Value**: It's the account name in Netlify where your site is hosted, like `foobar.netlify.app`

Usually, a website will have at least two records, one for the "naked" domain and other for the `www` domain and best practice is to use both.  After copying, use them to create new DNS records (`CNAME`) in CloudFlare.

{% admonition(type="note", title="Note") %}
If you have more subdomains, make sure to properly add them in CloudFlare's DNS settings.
{% end %}

After saving and continuing, CloudFlare will inform its name server URLs.  Take note of them, go to your domain name registrar's dashboard and change the current DNS data for CloudFlare's.  Then, return to the CloudFlare dashboard and inform that you've changed the name servers, so it'll ask you to enable some security and optimization features for the domain and inform that the changes will take up to 24h to take place --in my case, it took &approx;15 minutes for the changes take place.


## Testing

You can monitor the changes with httping, but I don't recommend pinging indefinitely the domain because it can be confused with a DoS attack.  Instead, run the command now and then to check if the servers changed.  If you're not so anxious than me, just wait for CloudFlare's email confirming that the configurations were applied.

At this point, your site should be accessible through CloudFlare and it'll be transparente for the visitors, while your site's IP address will be owned by CloudFlare.  From now on, CloudFlare will intercept all traffic to your domain and you'll be able to monitor and secure your environment with security tools from CloudFlare, like WAF, DDoS protection, and Bot Fight Mode.

{% admonition(type="tip", title="Pro Tip") %}
Make sure to test your site in your browser's incognito mode, because it'll avoid using any local cache, thus showing almost the same content your visitors will get.
{% end %}


## Updates

- **2023-09-22:** If the whois data is already pointing to the new name servers and the tests are pointing to the old servers, maybe you should clear your DNS cache. In macOS, it can be accomplished with these commands: `sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder`
