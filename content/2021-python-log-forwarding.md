+++
title = "Real-time Log Forwarding with Python and Syslog"
date  = 2021-09-01
description = "Create a smart log forwarding script using Python and Syslog."

[taxonomies]
tags = ["python", "logging", "unix", "syslog"]
+++

Recently, I had to solve this problem: Having a system that generates some log files, to send such logs to our SIEM, considering that this system had no integration with Syslog.  I solved this problem some time ago by writing [a shell script](https://gist.github.com/lopes/81b90d86b30e2730df241c90cc323837) to read all log files of the day before and send all at once to the SIEM.  This approach worked fine, but the downside is the lack of recent data --I was always looking to the past.

This time I decided to create a smarter version of the script using Python which I named [teslacoil](https://gist.github.com/lopes/a0366fc6a231eee38973e7e3e18c91e5), so the logs could be pushed to the SIEM [almost] in real-time.  The algorithm would be quite simple:

1. For the first turn, create a copy of the source log file in a working directory and send the whole content to Syslog.
2. For the next turns, read the source file, make a diff with the copy of this file in the working directory, send the diff lines to Syslog, and add them to the auxiliary file.
3. By the end of the day, the source log file is rotated, so the script should measure the source file size and if it is less than the auxiliary file size, repeat step #1.

The process of writing the script in Python was very fast, but I had to deal with some problems related to the encoding of the source files.  The Python codec was not able to read certain characters and so it throwed an `UnicodeDecodeError`.  Since I still could not debug this issue, I put those lines inside a `try/except` block, logging the file with an error message.

In this script I used the [Duck Typing](https://en.wikipedia.org/wiki/Duck_typing) approach, avoiding too much validation, thus letting the exceptions flow and dealing with them appropriately.  This kind of approach tends to reduce the number of lines and make the script faster.  I was expecting to run it every five minutes, but it was so fast that it is running every minute.

So, now I have logs for every minute instead of every day, which is great.   In the new script I also used the [configparser](https://docs.python.org/3/library/configparser.html) module for Python that allowed me to decouple the configuration data from the source code.


## Deploying
This code was deployed in an OpenBSD server and the first thing I did was to create a user to run the code: `teslacoil`.  Since the log files had reading permissions from everyone in the system, this user would be able to process them with no prior configurations.

Next, I made three directories in the user's home (`mkdir {bin,etc,tmp}`):

- `bin`: Home for `teslacoil.py`.
- `etc`: Home for `teslacoil.conf`.
- `tmp`: Working directory, home for auxiliary files.

Put the `.py` and `.conf` files in their directories and granted the script execution permissions.  Then, I set the path for the configuration file in the script (full path) and set the path variables in the script.  Finally, added the script in crontab:

```
* * * * * /home/teslacoil/bin/teslacoil.py
```

In the end, I disabled the `teslacoil` user password so I could only `su` to him.  This can be done in OpenBSD with the `vipw` command and replacing the password hash for the user with **thirteen** asterisks (`*`).

And that's it!  Script up and running with the least privileges.  So far, it is performing so good I'm considering running it twice a minute.  :)

Let's go!
