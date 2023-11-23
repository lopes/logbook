+++
title = "Query Security Services for IP Reputation"
date  = 2022-09-06
description = "Learn how to query three security services in one shell script to check IP reputation."

[taxonomies]
tags = ["unix", "security", "networking"]
+++

**tl;dr**: Use [this script](https://gist.github.com/lopes/8bcb3294b3ede9a5e0da71ff64d8843a) to query three of the best security services on the internet about security-relevant data on IP addresses.

It is common for Information Security Engineers to check if a given IP address is good or malicious and [maybe] that's why there are so many services around the web that provide such information.  Despite that, usually engineers stick with one or two of that services in their daily activities.  Three of the most common services I had contact was [the order does not matter]:

1. [VirusTotal](https://www.virustotal.com/)
2. [AbuseIPDB](https://www.abuseipdb.com/)
3. [GreyNoise](https://www.greynoise.io/)

Since I was wishing to make a programming task, I decided to write a shell script to query all of those three services at once for me, saving some time during my day.  I thought about doing it in Python first, but the three services I chose had straightforward examples in [curl](https://curl.se/), I did it in Shell Script.


## Scripting
The script is available as a Gist in GitHub [here](https://gist.github.com/lopes/8bcb3294b3ede9a5e0da71ff64d8843a).

What I've done was query each service using curl and parse the result (always in JSON format) to get the attributes I wanted to get from each one.  To normalize the JSON, I send the output of curl to [tr](https://en.wikipedia.org/wiki/Tr_(Unix)) so newlines are removed and spaces are squeezed (I hate to pipe to the same command twice in a line (thanks to a former teacher), but `tr` does not allow to use more than one flag at a time).  Parsing is done using regular expressions in [sed](https://www.gnu.org/software/sed/manual/sed.html).  `sed` is a bit harder to use than [grep](https://man7.org/linux/man-pages/man1/grep.1.html) and [Awk](https://www.gnu.org/software/gawk/manual/gawk.html), but it has more features than the first and is faster than the second --Perl and Python were out of thought to me.

{% admonition(type="note", title="Note") %}
The major downside of using regexes in `sed` is that it does not implement the [non-greedy operator](https://www.computerworld.com/article/2786107/regular-expression-tutorial-part-5--greedy-and-non-greedy-quantification.html) (`?`), which is very useful for many sorts of applications.
{% end %}

The script's internals is organized in comments, global variables, functions, and main block.  I opted to write an argument parsing function instead of using [getopt](https://stackabuse.com/how-to-parse-command-line-arguments-in-bash/) because I thought it would be more interoperable and it would be easier for me to reuse that code.  The script will look for environment variables to get the API keys for each service, but the user can pass them through command line arguments.

I split the request and parsing of data (thanks to a co-worker's idea) to facilitate future maintenance, thus there is one request function and three parsing functions, one for each service.  Finally, the script will check which API key is available and will use those that were informed.


## Usage
The usage is quite simple, thanks to the `argparse` function.  Set the API keys in your environment under your `.zshev` for example or pass them through the CLI arguments or even use them both ways.  For instance, to set the environment variables globally:

```sh
export APIKEY_ABUSEIPDB="AIPDB_APIKEY_HERE"
export APIKEY_VIRUSTOTAL="VT_APIKEY_HERE"
```

The CLI arguments follow the pattern `--service-key <KEY>` and can be used like this:

```sh
checkip.sh --virustotal-key VT_APIKEY_HERE --greynoise-key GN_APIKEY_HERE 8.8.8.8
```

If you set all variables globally in your profile, you can just call the script and the IP address you want to check:

```sh
checkip.sh 8.8.8.8
```

If you have a list of IP addresses to check, put them in a text file, one per line.  Then, list the file, piping the output to a `while` loop which will read each line as an IP address, run the script for that address, write the output in a text file, and print it on the screen at the same time --[tee](https://en.wikipedia.org/wiki/Tee_(command)) command is awesome.

```sh
cat ipaddr-list.txt | while read ipaddr; do checkip.sh $ipaddr; done | tee out.txt
```

If you have a list of few addresses and want to be fileless, use a `for` loop instead:

```sh
for ipaddr in 1.0.0.1 1.1.1.1 4.4.4.4 8.8.8.8; do checkip.sh $ipaddr; done
```

And that's it!  A project like this is good in many ways, like saving time in the future, learning a couple more things, and to dusting off programming enthusiasts.

Go ahead!  Avante!
