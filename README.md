# solo - prevent simultaneous instances of a *nix job

solo is a very simple script (10 lines) that prevents a program from running more than one copy at a time.  It is useful with cron to make sure that a job doesn't run before a previous one has finished.

Usage:

    solo -port=PORT COMMAND

where

    PORT      some arbitrary IP port number to be used for the locking (1...65535)
    COMMAND   shell command to run

options

    -verbose  be verbose
    -silent   be silent

Use it like this:

    * * * * *  solo -port=3801 ./job blah blah

The script "job" will run every minute, but only if the previous one has finished.
(Of course, you can use different cron parameters to run it other than every minute.)

Port 3801 in the example is an arbitrary TCP port number that isn't used elsewhere.
solo works by binding to 127.x.y.1:port (see below).
If the chosen port is not free,
then a previous instance must still be running, and the job is aborted.
Make sure to use a different port number for each job, or they will prevent each other from running.
If you aren't running as root, the port number should be above 1023.  The maximum allowed port number
is 65535.

The loopback IP address 127.x.y.1 is used, where xy is the uid.
This way, different users will never use the same ports even if they use the same port numbers.

(NOTE: OpenBSD doesn't allow 127.x.y.1 IP addresses.
See the comment in the code, which changes the IP address to 127.0.0.1.
It will work, but it will interlock between users.
Thanks www.gotati.com.)

The -silent switch tells solo to silently terminate when a previous instance is already running.  Without -silent, a message is printed to stderr, indicating that a previous instance is already running.

Running a job this way (using crontab and solo) is much more maintainable than other methods:

1. you don't need root privileges</li>
1. you avoid the complexity of /etc/rc.d files</li>
1. you don't have problems if the job crashes (cron restarts it)</li>
1. you can run multiple versions, one per directory, so, for example, you can have separate dev, stage, and live directories</li>

A nice trick is to create a cron job:

      * * * * * solo -port=3802 find . -iname makefile -exec fgrep --silent repeatedly: {} \; -execdir make --silent repeatedly \;

This cron job will find each Makefile and execute its "repeatedly" target.  Thus you can cause a job to run periodically by simply adding a target to a Makefile.  The "-exec fgrep ..." step makes sure that
the Makefile contains the target before "-execdir make ..." runs it, so error messages are avoided.

If you like, you can create multiple jobs that run targets named "repeatedly", "hourly", "daily", etc., to run jobs at different intervals.

**Acknowledgements**

Thanks to Adam Fritzler for pointing out that the loopback interface is a /8.

**Author**

Timothy Kay <a href=mailto:timkay@not.com>timkay@not.com</a> +1-650-248-0123
