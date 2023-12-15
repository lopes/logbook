+++
title = "Testing The Logfile Navigator"
date  = 2023-12-15
description = "Exploring log analysis with lnav tool: challenges, insights, and practical tips."

[taxonomies]
tags = ["threatdetection", "security", "csirt"]
+++


The other day I stumbled upon a promising tool to analyze logs: [The Logfile Navigator](https://lnav.org/) (lnav), and as once in a while I have to analyze log files, I decided to give it a try.  To make things more fun, I decided to test it using a challenge available on the internet, like a CTF one.  Well, I found this "[Hammered](https://cyberdefenders.org/blueteam-ctf-challenges/42)" challenge and I had a tool to learn.  Here I'm sharing my journey as well as some impressions and personal opinions over the tool.  Let's go!


## Proof-of-Concept
In the next subsections, I'm sharing commands and impressions for each question in the challenge, but avoiding unnecessary spoilers.

**Q1, Q3: Service used to gain access and name of the compromised account**  
lnav was useful because of its syntax highlight and overall interface.

**Q2: OS version of the targeted system**  
Just opened the correct file in lnav and the OS version was in the first lines, but if I couldn't find it, knowing the OS family (Linux), I could run the following command to go straight to the line: `/Linux version.*\(gcc version`.

**Q4: How many adversaries did access the system**  
I started analyzing the Apache Access Logs to check available fields and to count the IP addresses:  

```sql
;select * from logline
;select c_ip, count(c_ip) from logline group by c_ip order by count(c_ip) desc
```

After a few tries, I got some tips but kept stuck.  Only then I noticed this question was poorly written because the goal is not clear. I noticed in the comments that other people had trouble here too.  Then, I res restarted the investigation with `control-r` to get two lists, one of IPs that accessed the system and the other with the IPs that tried to access and failed (suspicious).  Crossing the two lists manually as they were short, put me in the right direction.

I generated the first list with two commands: 

```sql
:filter-in sshd.*Accepted.*root
;select distinct col_0 from logline order by col_0
```

I had trouble generating the second list in lnav because the IP address was not alone in the column and I wasn't able to extract it, so I went more old school:

```sh
grep "sshd.*failure.*rhost=.*user=root" auth.log | sed -ne 's/^.*rhost=\([0-9.]*\).*$/\1/p' | sort -n | uniq
```

**Q5: IP addresses the adversary used to successfully log into the system**  
This filter in lnav: `:filter-in sshd.*Accepted.*root`

**Q6: The number of requests to the Apache server**  
This one should be pretty straightforward, but I noticed a bug was preventing lnav to show all log lines from Apache, so I had to solve this trivial one with: `wc -l`

**Q7: How many rules were added to the firewall**  
Simple filter in lnav`:filter-in tables -A`  

{% admonition(type="tip", title="Pro Tip") %}
New firewall rules added in-line are logged in auth, like SSH.
{% end %}

**Q8: Scanning tool used**  
Simple search (`/`) in lnav for the name of the tool in the correct log file.

**Q9: Last adversary login with IP address 219.150.161.20**  
lnav filter: `:filter-in sshd.*Accepted.*219.150.161.20`

**Q10: Most important warning message from the database**  
Found this one using `grep -l "sql" *` (inside the folder with the logs).  Then opened the log file with lnav and filtered the logs to investigate: `:filter-in sql`

**Q11: Account created on April 26th**  
Easy peazy with: `:filter-in Apr 26.*new user`

**Q12: Proxy's user-agent used by the adversary**  
Listed all distinct user-agents that hit Apache: `;select distinct cs_user_agent from logline order by cs_user_agent` Then analyzed the output to possible proxies.  If I knew about the pattern before (`p.*`), I could use this command: `;select cs_user_agent from logline where cs_user_agent like "p%"`


## Wrap Up
### Useful hotkeys
[This hotkey map](https://docs.lnav.org/en/latest/hotkeys.html) is gold, but here's my list.

- `/`: to type a search query
- `;`: to type SQL
- `:`: to enter the filter mode
- `g`: go to the top
- `G`: go to the bottom
- `space`: next page

### Pros
- lnav saves sessions, so if you're in the middle of an investigation and you closed the program involuntarily, reopening the file will take you to the same point where you were.
- Command autocompletion resembles Cisco: Typing `:filing` for example will autocomplete to `:filter-in`.

### Cons
- Few users, few help, few documentation.
- A very strange bug preventing some lines to be shown in one Apache log file (maybe a problem in parser? 🤔)
- The interface confused me sometimes as it was not clear if a filter was applied or not.


## Opinions
lnav is the kind of tool that makes you wonder why nobody did that before.  Log analysis is not just useful for Infosec Analysts but also to System and Network Administrators, and a plethora of IT professionals.  <mark>It's past time to have decent tools to help us troubleshoot environments and perform forensic investigations.</mark>

My overall feeling is that lnav is a tool in maturation, so it presents a couple of bugs and lacks some features and documentation.  I think it follows the idea of applying filters over filters, like piping in CLI, but it's not clear through the interface or documentation.  With more users and developers, it certainly can grow up to become a standard tool in many Unix systems.  For sure **I added this to my toolbelt**.

{% admonition(type="info", title="Info") %}
[Here's](https://www.youtube.com/watch?v=RurEIYFZyZI) another good lnav review.
{% end %}


## Bonus: The Challenge
Just some thoughts to help you tackle the challenge (and other log analysis tasks).  First, understand the scenario: List the logs available and based on the tools, make a mental map of the adversary's exploit path.

In the Apache logs, learn what IP addresses tried to exploit the services and what resources were targeted.  Write down any suspicious IP and targeted resources.  Then, move to the auth logs to check remote access and take note of suspicious IPs.

The auth logs will give you information enough to move some IPs and usernames from suspicious to a new "malicious" list.  Having these two lists, suspicious and malicious, will help you navigate in the logs because in essence they're both Indicators of Compromise (IOCs) pending confirmation and confirmed.

These IOCs can be used to link actions across different tools (aka., log files) and describe the actions performed by the adversaries.  In this sense, having the actions disposed in a timeline is essential and here you must be aware of time zones because not all systems may be in the same tz or (worse and more frequent) this information is missing and you must infer when an action happened in comparison to others.

In summary, perform a discovery in the first few moments and define a hypothesis for compromise.  Investigate the logs in the sequence of your hypothesis taking notes for IOCs and their statuses (investigating, confirmed, clear) and mount the sequence of actions considering the log timestamp and timezone.
