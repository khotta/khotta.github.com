---
layout: sr/default
title: About
place: first
en: yes
---

{% include sr/btn.html link="document.html" text="See the reference manual" width="20" %}

> ## Features
> * SimpleRotate is awfully simple and easy to use.
> * It is thread safety.
> * SimpleRotate class is singleton design pattern.
> * It is possible to write logs to a log file safety even when multiple processes write logs to the same log file at the same time.
> * It is possible to write logs with format that your preference. You can also change date format as you prefer.
> * It is possible to make vulnerability levels with logs when write logs to a log file.
> * Logs have some vulnerability levels and you can choose logs to write to a log file by vulnerability levels. 
> * When the log file reaches a specific size or specific term, SimpleRotate renames the log file and creates a new one.
> * Renamed log files are rotated with specific counts. So there is no necessary to mind lack of storage capacity on server.
> * It is possible to compress renamed log files when you need that.
