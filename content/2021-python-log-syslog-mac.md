+++
title = "Logging Python Messages to Syslog in macOS"
date  = 2021-08-17
description = "Send log messages from Python to Syslog in macOS."

[taxonomies]
tags = ["python", "logging", "unix", "syslog", "apple"]
+++

Here, I will describe the steps I took to send messages from a Python script to Syslog and how to monitor those logs in macOS.  Well, Python (3.9) comes with the `Syslog` module, which handles the process of creating log entries and it is really simple and straightforward.  For instance, the line below creates a critical Syslog message:

```python
import syslog
syslog.syslog(syslog.LOG_CRIT, 'hello, syslog!')
```

In my experience, I had to run Python in unbuffered mode passing the `-u` option to the interpreter.  Then, to check log files, I noticed that macOS does not work with Syslog like other similar systems.  It comes with the `log` utility that has some commands to work with logs.  Using the `stream` command with the right parameters and filtering the Python process, I was able to see all logs coming in [almost] real-time when the script was executed (almost like in `tail -f`).

```sh
log stream --info --debug --predicate "process == 'Python'"
```

And that's it!  Logging like a boss!  :)


## References
- [Stack Exchange](https://unix.stackexchange.com/questions/68059/daemontools-multilog-loses-log-line-time-information-how-to-fix-it)
- [Python: Syslog](https://docs.python.org/3/library/syslog.html)
