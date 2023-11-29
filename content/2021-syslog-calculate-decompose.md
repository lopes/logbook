+++
title = "How to Calculate and Decompose Syslog Message Priority"
date  = 2021-09-02
description = "Calculate and decompose Syslog message priority."

[taxonomies]
tags = ["logging", "syslog"]

[extra]
math = true
+++

When dealing with [Syslog](https://success.trendmicro.com/solution/TP000086250-What-are-Syslog-Facilities-and-Levels), one should notice that each message starts with a number.  This number identifies the priority of that message, and in this text, I will explain how to calculate and decompose it.  Here are some examples of Syslog messages:

```
<11>Aug 23 19:07:55 <process>: <payload>
<13>Aug 23 19:08:55 <process>: <payload>
<84>Aug 23 19:09:55 <process>:  <payload>
<96>Aug 23 19:10:55 <process>:  <payload>
<136>Aug 23 19:11:55 <process>:  <payload>
```

That first number at the beginning of the line (`^<([0-9]*)>`) is the **priority** value (**prival**) and it ranges from 0 to 191.  The prival is obtained from **facility** and **severity** values:

- **facility** is the <mark>process that created the message</mark> and it varies between 0 and 23, where 0-15 are predefined and 16-23 are commonly used by networking equipment
- **severity** values are <mark>predefined</mark> and they vary from 0 to 7

The following equations show how to calculate the **prival** and how to get the **facility** and **severity** values from the **priority**:

$$
    prival = facility * 8 + severity
$$
$$
    severity = prival \mod 8
$$
$$
    facility = \frac{prival - severity}{8}
$$

When working with Syslog, it is useful to know how to decompose the priority to troubleshoot the current configuration.  By the way, here are the severity and facility values for the five priorities shown at the beginning of this text:

```
priority=11,  facility=1  (user-level),    severity=3 (error)
priority=13,  facility=1  (user-level),    severity=5 (notice)
priority=84,  facility=10 (security/auth), severity=4 (warning)
priority=96,  facility=12 (ntp),           severity=0 (emergency)
priority=136, facility=17 (local1),        severity=0 (emergency)
```

That's it! :)
