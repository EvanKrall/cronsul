# Cronsul

Cronsul is a very simple distributed periodic job scheduler, built on
[cron](https://en.wikipedia.org/wiki/Cron) and [consul](https://consul.io/).

## Problem Statement

Cronsul aims to solve a problem of:

 - I want to run a task periodically
 - My task may run on any one of a set of boxes
 - I want my task to run, even if some of those boxes are down.
 - I do not want my task to run more than once for a given period.

Cronsul does not currently attempt to solve any of the following problems:

 - My task should be retried if it fails.
 - My task is resource-intensive and needs to be scheduled on a box that has
   enough resources to spare.
 - I would like to run a task continuously.

## Usage

`cronsul` is intended to be run by your cron daemon. You should schedule the
same cronsul command on multiple boxes, at the same time. `cronsul` will
attempt to grab a lock, and will exit silently if it fails to grab this lock;
this ensures that only one box will run the command specified for a particular
period.

A typical cronjob entry might look like this:

```
0 * * * *   root    cronsul cleanup_logs /usr/local/bin/cleanup_logs.sh
```

The `cronsul` script itself takes one or two arguments, in addition to a command:
1. A task id. This is used to associate multiple `cronsul` runs from different
boxes.
2. Optionally, using `--period`, the length of the period (in seconds) during
which you don't want your command to be run twice. This defaults to 60, which
should generally be enough, but if your cron job might be started in different
minutes on different boxes (e.g. due to different cron jobs or an overloaded
box), you may increase this up to the frequency at which your job runs.

The current Unix time is rounded down to the next lowest period, and this
value is used to calculate the key where `cronsul`'s lock lives, so a lock
created at 17:54 with a period of 3600 will expire at 18:00.
