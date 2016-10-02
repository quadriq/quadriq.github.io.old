---
layout: post
title:  "Run cron-jobs inside docker container, CentOS 6"
comments: true
date:   2016-10-02 12:00:00 +0000
permalink: /:year/:month/:day/:title/
---

Here is how to configure cron daemon and rsyslog(to show cron logs) inside a docker contaner, based on CentOS images.

### Inside container

Change into contaner with `docker run -it CONTAINER-ID bash`. 

```bash
# First we need to install some packages
yum -y install vixie-cron rsyslog

# comment out a line inside rsyslog conf, containing $modLoad imklog
sed -i '/$modLoad imklog/s/^/#/' /etc/rsyslog.conf

# startup services
/etc/init.d/rsyslog start
/etc/init.d/crond start
```

### In Dockerfile

To do this in advanced you could do it in `Dockerfile` and then start it with `supervisord`.

First create a `supervisord.conf`

```
[supervisord]
nodaemon=true

[program:rsyslog]
command=/sbin/rsyslogd -i /var/run/syslogd.pid -c 5

[program:crond]
command=/etc/init.d/crond start

```

The Dockerfile would be then like:

```
FROM centos:6

RUN yum -y install vixie-cron rsyslog
RUN sed -i '/$modLoad imklog/s/^/#/' /etc/rsyslog.conf

RUN yum install -y net-tools python-setuptools hostname inotify-tools yum-utils
RUN easy_install supervisor

ADD supervisord.conf /etc/supervisord.conf

ENTRYPOINT ["/usr/bin/supervisord"]
```

That's it! :)
