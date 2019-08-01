---
title: How to send Zenaton logs to performance monitoring systems
description: 'Article about how to send Zenaton logs .... '
slug: zenaton-news
date: 2019-07-30T15:33:23.748Z
author: François
author_role: Senior Software Engineer
category: Zenaton News
title_seo: todo
description_seo: todo
twitter_card: todo
---
Monitoring your production is the cornerstone of an effective modern computer system, and at Zenaton we understand that our customers want to have as much information as possible regarding their various tasks and workflows. In this regard, the Zenaton dashboard itself offers some very useful performance indicators.

If you want to audit how things are going on on your servers, another source of information is the pair of files zenaton.out and zenaton.err created automatically when you run the zenaton listen command. They contain respectively whatever data your tasks and workflows write in the standard output and error of your system.

While those files are useful, they are not always simple to consult since a production server is not meant to be widely available for anybody to access. We’ve had recently some feedback that many of you wish to have a possibility to incorporate those files into their favorite performance monitoring solution.

> Note that we have plans to add a live tail of the content of those files on the Zenaton website itself, but we have no ETA to disclose yet.

In this article, I am going to show you how to forward the contents of the Zenaton log files into two of the most commonly used performance monitoring systems: Datadog and the ELK stack.

# Datadog

I will assume that you have already installed the Datadog Agent on your machine.

Locate the Datadog configuration file (mine was in /etc/datadog-agent/datadog.yaml), and if it’s not already done, un-comment the logs_enabled line and set its value to true (it is false by default on a fresh install).

Then locate your Datadog Agent configuration directory, add a sub folder named zenaton.d in it, and in this new folder, add a file named conf.yaml.

You might have to set the permissions on those new folder and file so that the dd-agent user and the dd-agent group have access to them.

```
logstype: filepath: /PATH_TO_YOUR_ZENATON_WORKER/zenaton.outservice: NAME_OF_YOUR_DATADOG_SERVICE source: custom type: file  path: /PATH_TO_YOUR_ZENATON_WORKER/zenaton.errservice: NAME_OF_YOUR_DATADOG_SERVICEsource: custom
```

![monitoring datadog](/uploads/1_qrmcqsd8wago-rd1upwp9q.png "Datadog monitoring")

## Conclusion

Datadog and the ELK stack are two of the most common performance monitoring systems out there, but of course there are many others, and covering them all would be difficult. Most of them propose an out-of-the-box solution to forward log files.

We expect to be able to propose new options to give you an easy access to your Zenaton log files in the foreseeable future. If you have ideas, make sure to share them with as building software that makes our customers successful is our top priority.

Don’t hesitate to reach out to the Zenaton team if you encounter any difficulty setting up Zenaton with any other third party system. We’re always glad to lend a hand.
