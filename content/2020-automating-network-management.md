+++
title = "Automating Network Management with NetBox Scanner"
date  = 2020-05-31
description = "Streamlining network management with IPAM and a network scanner."

[taxonomies]
tags = ["python", "git", "automation", "networking"]
+++

Back in 2018, I was leading the SOC and NOC teams and although I'm not an excellent network engineer, I always tried to help technically the networking team.  On that year we were struggling with the [IPAM](https://en.wikipedia.org/wiki/IP_address_management) tool and started looking for a replacement.  Then we found and tested [NetBox](https://github.com/netbox-community/netbox), an open-source IPAM from [DigitalOcean](https://www.digitalocean.com/), and put that into production.

The main concern the NOC had about IPAM is that this tool can be outdated very fast, so I decided to write a tool to automatically discover and update certain subnets.  This led to the [netbox-scanner](https://github.com/lopes/netbox-scanner) creation and when finished, this software scanned daily more than 50 subnets, some of them a /21, which resulted in lots of hosts registered and managed automatically.

As the company changed, I left the position of NOC leader focusing on SOC.  So the netbox-scanner was forgotten by both teams although some people were still using it --source code was available on GitHub.  Despite continually receiving issues and pull-requests, I had no time to improve that code, so I neglected all requests.  It last until May 2020, when I decided to use some spare time due to [COVID-19](https://en.wikipedia.org/wiki/COVID-19_pandemic), to create a brand new version of that script.

At first, I planned the new version, focusing on a modular environment, and documented this in a new issue.  After that, I started the repository cleaning process, reviewing all issues for some ideas that could have been added to the project --the feature to use the configuration file from two possible directories was born here.

When the technical part began, I thought it would take at least one month to finish the job since I hadn't been coding for almost 6 months, and I still had my full-time job, so I would code only when idle.  To my surprise, all the concepts were fresh in my mind, so I was able to finish the job in only three days, which made me really happy!

Despite this project is not directly secure-related, it was very good to learn or improve some techniques:

- [Logging](https://realpython.com/python-logging/): Using logging to record actions and enable further tracking, good for fully automated scans.
- [Argument parsing](https://realpython.com/command-line-interfaces-python-argparse/): One of the best ways to catch user interactions is directly from the command line.
- [Config files processing](https://docs.python.org/3/library/configparser.html): Although the CLI is good, when there are many options, it is easier to use configuration files.
- [XML processing](https://docs.python.org/3/library/xml.etree.elementtree.html): Either XML and JSON are excellent standards for interoperability and it is always good to not reinvent the wheel.
- [API usage](https://pynetbox.readthedocs.io/en/latest/): NetBox has a good API, the [pynetbox](https://github.com/digitalocean/pynetbox), and it implements all actions I needed albeit the documentation is not good.
- [API creation](https://github.com/lopes/netbox-scanner/blob/master/nbs/prime.md): Since I decided to create an integration with Cisco Prime and couldn't find a Python API, I had to create my own --in this case, I used the one I wrote in 2019.
- [Testing](https://realpython.com/python-testing/): Automated tests improve the development lifecycle and enable [CI](https://en.wikipedia.org/wiki/Continuous_integration)/[CD](https://en.wikipedia.org/wiki/Continuous_delivery)/[DevOps](https://en.wikipedia.org/wiki/DevOps) functions --automated tests should include some security tests too, see [DevSecOps](https://www.redhat.com/en/topics/devops/what-is-devsecops).
- [Git features](https://backlog.com/git-tutorial/branching/create-branch/): Git is the best software I know for version control and here I had the opportunity to use some features I had never used --branch and merge.

All in all, it was fun and very satisfying to upgrade the netbox-scanner version, and consequently, I learned a lot of things.  I think this is the core concept of [free software](https://en.wikipedia.org/wiki/Free_software) when you use something and is able to contribute to that.

Let's move to the next objectives!
