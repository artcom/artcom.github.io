---
layout: post
title: Self-hosted project management tools
author: <a href="https://github.com/j3nsm4n">Jens Eraßmy</a>
excerpt_separator: <!--end-of-excerpt-->
---

Physical boards are most flexible when it comes to process needs. That changes when external collaboration and remote access are mandatory. Here is a short survey of requirements (defined by a developer) and potential tools.

<img style="margin-bottom:15px;" src="{{site.url}}/images/2018-01-02-project-management-self-hosted/board.png"/>
<!--end-of-excerpt-->

## What we need

Board:
- Tickets
- Configurable columns (best with sub-columns)
- WIP (work in progress) limits

Ticket:
- Description
- Comments
- Attach media
- Set relations to other tickets
- Use colored tags to categorize
- Assignable
- Subscribe to changes

What else:
- Communcication channels priority: Ticket > Email > Chat
- Wiki / Documentation
- Process for code reviews
- Groups of tickets (e.g. Milestones)
- User management

The communcication of all stakeholders should converge in the corresponding tickets.
It's always frustrating to collect communcication parts from different sources, like
email, chats and comments. Also, when external collaborators participate in the project, they should be easily
integrated into the process to prevent communcation desaster.

## What we don't need in tickets

- Progress: Tickets should comprise small tasks (max. 2-3 days)
- Enforcement of daily status updates
- Deadlines: A ticket comprises a small task. Multiple tasks can be wrapped in a milestone,
which can be used to track the overall progress and set due dates
- Documentation: Produces too much overhead and we consider it the wrong place. Completed tasks should be
documentated in a separate document (e.g. Wiki). Progress and impediments should be discussed in the Daily Stand Ups.
Decisions should be updated in the ticket description, not as comment.

## Overview

|                               | Redmine + Agile Plugin      | GitLab| Taiga | Kanboard  | JIRA  |
| ----------------------------- |:---------------------------:|:-----:|:-----:|:---------:|:-----:|
| WIP Limits                    | √                           | x     | √     | √         | √     |
| Configurable columns          | x                           | √     | √     | √         | √     |
| Subscribe to ticket changes   | √                           | √     | √     | x         | √     |
| Tags                          | (x)                         | √     | √     | √         | √     |
| Access managment (roles)      | √                           | √     | √     | √         | √     |
| Related issues                | √                           | x     | √     | √         | √     |
| Free pricing                  | x                           | (√)   | √     | √         | x     |

## In detail

### [Redmine + Agile Plugin](https://www.redmineup.com/pages/plugins/agile)

No possibility to configure columns per board (project). Here, columns correspond to
a "status". But a "status" is an overall attribute for all projects and only can be edited by the
administrator.

Furthermore there are no options to set mulitple tags per ticket. "Categories" which are supported are not
sufficient, because it is only allowed to set one per ticket.

### [GitLab](https://about.gitlab.com/)

Gitlab offers one issue board per repository. Unfortunately, we cannot set WIP limits.
Relations to other tickets are only available in an Enterprise Edition with costs.

### [Taiga](https://taiga.io/)

Taiga offers most of the required features, but the offered processes are very strict and tailored for Scrum.
E.g. a Kanban board can only contain user stories. Usability is also improvable.

### [Kanboard](https://kanboard.net/)

Free open-source tool, which offers a lot of the required features. It's very slim and has low requirements.
Because it's missing a possibility to subscribe to changes and it's visual backwardness, it's more useful as a
Personal Kanban Board.

### [JIRA](https://www.atlassian.com/software/jira)

The tool we would prefer, providing all required features and more. But it is with costs.

## References
* <https://about.gitlab.com/2016/03/03/start-with-an-issue>

## Disclaimer

This article is subjectively biased and does not speak for every developer @ ART+COM.
