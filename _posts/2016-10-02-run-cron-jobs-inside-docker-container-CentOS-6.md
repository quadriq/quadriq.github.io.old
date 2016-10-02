---
layout: post
title:  "Run cron-jobs inside docker container, CentOS 6"
comments: true
date:   2016-10-02 12:00:00 +0000
permalink: /:year/:month/:day/:title/
---

Here is how to configure cron daemon and rsyslog(to show cron logs) inside a docker contaner, based on CentOS images.

Change into contaner with `docker run -it CONTAINER-ID bash`. 

```bash
# First we need to install some packages
yum -y install vixie-cron rsyslog

# comment out a line inside rsyslog conf, containing $ModLoad imuxsock
sed -i '/$ModLoad imuxsock/s/^/#/' /etc/rsyslog.conf

# startup services
/etc/init.d/rsyslog start
/etc/init.d/crond start
```
