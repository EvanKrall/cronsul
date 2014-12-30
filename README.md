Cronsul is a very simple periodic job scheduler, built on [cron](https://en.wikipedia.org/wiki/Cron) and [consul](https://consul.io/).

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
