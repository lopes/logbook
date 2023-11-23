+++
title = "Productivity Unleashed"
date  = 2023-11-14
description = "Unlock seamless productivity with strategic tools and a 2-step system for efficient organization and focus."

[taxonomies]
tags = ["productivity"]
+++


In 2023, I encountered a significant challenge while deploying a SIEM.  Drawing from past experiences, I understood the magnitude of managing data and tasks for a successful project.  This motivated me to reassess my office tools for enhanced productivity and to prevent oversight.

After thorough research, I came across productivity gurus' videos showcasing intricate processes and tools.  While acknowledging their valuable insights, I aimed to streamline the process and reduce the number of tools, easing the management burden and allowing more time for productive tasks.

Fortunately, Jeff Su had faced a similar challenge and shared his simplified productivity process in [this video](https://www.youtube.com/watch?v=7M6bIeVbCqA).  I adapted his approach, incorporating insights from [Matt D’avella’s video](https://www.youtube.com/watch?v=0_44XEVOwek) and adding my personal touch.  In this post, I'll present the 2-step productivity system (**Capture** and **Organize**) and how I leverage tools to achieve my goals.


## Capture
Capture involves acquiring new tasks, projects, or crucial data for personal and project use, essentially the input received from external sources.

### Browser
When browsing, I often come across useful articles that I can't read immediately.  Utilizing the [Instapaper plugin](https://chrome.google.com/webstore/detail/instapaper/ldjkgaaoikpmhmkelcgkgacicjfbofhh), I can easily highlight parts of a page or save a URL for later.  If the browser lacks the plugin or I can't connect to Instapaper, Todoist serves as an alternative to save information as a note for future organization (in Notion, Instapaper, or Google Drive).

### Todoist
[Todoist](https://todoist.com/) serves a singular yet crucial purpose: Note-taking.  Its efficiency in creating new notes due to its fast app and widgets makes it highly recommended.  Additionally, it seamlessly integrates with Google Calendar, automatically creating events on my calendar and recognizing due dates in notes.  I ensure to create atomic tasks, initiating each task with a verb to indicate the required action.

### Gmail
As a gateway for tasks and content, Gmail plays a vital role, especially for newsletters.  I use Gmail and leverage the plus sign and a string to direct content, enhancing future organization (example: I sign newsletters using an address like `my.user.name+newsletter@gmail.com`).  I'll go into more details on Gmail in the next section.


## Organize
Once data is received, effective handling is necessary to optimize its use.  The primary goal is to sort data based on where it will be utilized, not just its source (following Tiago Forte's quote).

### Gmail
Emails (Gmail user here) can be tricky due to the influx of useless messages.  To combat this, I unsubscribe from unnecessary notifications and heavily use the “Report SPAM” button.  I employ filters to label and manage frequent messages, such as newsletters, banking updates, and tool notifications.  The goal is to spend less time handling messages and more time on essential tasks with the Inbox Zero principle in mind.

#### Labels
Contextualizing messages with labels saves time managing the Inbox.  I use three priority labels: `High`, `Medium`, and `Low`.  Tagging frequent senders allows for efficient handling, distinguishing between urgent and non-urgent messages.

#### Mails for the Future
For messages requiring future action, two approaches are employed:

1.  **Snooze Feature:** Immediate action is deferred using the snooze feature.  This prevents cluttering the Inbox with unread messages for an extended period.
2.  **Task Creation:** For more detailed future actions, a task is created in Google Calendar or Todoist, enriched with relevant information.

#### Filters
[Filters](https://support.google.com/mail/answer/7190?hl=en) complement other strategies to automate actions and simplify my workflow.  Important filters include:

- **Newsletters:** Content sent to `my.user.name+newsletter@gmail.com` is forwarded to Instapaper, marked as read, and deleted.
- **High Priority:** Known senders requiring frequent action are tagged as high priority, marked as important, archived, and never sent to SPAM.
- **Medium Priority:** Mostly financial and streaming services, labeled and archived.
- **Low Priority:** "No reply" and "for your information" mails, handled similarly to Medium priority.
- **Unwanted:** Unsubscribe attempts that couldn’t be processed, marked as read, and deleted.

Periodically, a "Garbage Collector" filter deletes any unlabelled message older than 30 days.

{% admonition(type="info", title="Notifications") %}
Email notifications can be a bit bothersome, but certain ones are crucial—like the "new sign-in alerts."  Given that the High-priority filter bypasses the Inbox, you might miss out on notifications for these important messages.  To address this, I recommend adjusting the settings in the Gmail app on my phone (`Gmail > Settings > [your email address] > Notifications > Manage Labels`).  Here, I enable syncing for the High priority label and turn on notifications for new messages in this category.  Additionally, I ensure that notifications for other labels, including the Inbox, are turned off.
{% end %}

### Instapaper
[Instapaper](https://www.instapaper.com) serves as the destination for newsletters and interesting articles found during internet browsing.  It allows separation of reading time from browsing or mailing time and facilitates highlighting of text.  After reading, items are either deleted or archived based on their importance.

### Google Calendar
Google Calendar acts as my personal secretary, reminding me of appointments, meetings, and tasks.  Calendar sharing with Todoist ensures tasks are displayed in a timely fashion.

### Notion
Notion often serves as the next step for Todoist notes.  After making a quick note, I elaborate on it in Notion or add the link or idea to an existing page.  In Notion I follow the Tiago Forte's PARA Method, with one major page for each phase of that method.  [Read this article](https://fortelabs.com/blog/para/) for more information on the PARA Method.  Notion is where I write and review blog posts, record insights, and highlight information from books.

### Google Drive
For more robust file management, I turn to Google Drive (GDrive).  I implement the PARA method, using up to 5 subfolders from the root.  To reduce friction in finding and categorizing items, I aim for up to 3 subfolders.  An RGB-like color scheme shared between GDrive and Notion aids in faster retrieval: Red for Projects, green for Areas, blue for Resources, and gray for Archives.

### Slack
Used daily in my work, [Slack](https://slack.com/) can become overwhelming due to numerous channels.  Implementing a layered approach sorted by priority helps manage channels effectively. This approach consists of grouping the channels in many “layers” in priority order. In the end, I created eight layers, being the first one the most important (my team's channels) and the last ones the less important ones (channels used to track automatic updates from tools and deprecated yet not closed channels).  Also, the less important channels are muted to avoid interrupting me for useless updates.

{% admonition(type="tip", title="Pro Tip") %}
Muting less important channels prevents unnecessary interruptions.
{% end %}


## Takeaways
Months after implementing this organization, it significantly enhances my productivity.  Putting tools to work in an organized manner makes it fast and easy to find needed information or determine where to store it.  By minimizing notifications and being reminded of essential tasks, I can focus on what truly matters, yielding better results.

While the process and tools play a role, success ultimately depends on personal effort.  There's no magic; I must actively work to achieve my goals.  The process and tools merely serve as a structured approach, with me as the driving force.
