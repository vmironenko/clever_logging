clever_logging: smart gathers logs from multiple machines into one place in realtime 
clever_logging is further develobment of dklab_logreplica script from Dmitry Koterov (http://en.dklab.ru/lib/dklab_logreplica)\n"
(C)2015 Valery Mironenko vmironenko@thumbtack.net
License: GPL

This simple, but very handy and powerful tool allows you to filter and pull logs
from the whole cluster to a single logging server in realtime and run scripts when certain events is happened. These
logs may be used for monitoring, aggregation or analyzing.


SYNOPSIS
--------

1. Choose your logging server: it is a machine with 
   /opt/clever_logging/ directory holding clever_logging scripts.

2. Copy clever_logging.init into /etc/init.d/clever_logging and
   setup its automatical startup at server boot time.

3. Copy default config file clever_logging.conf.sample to
   /etc/clever_logging.conf and modify it according to your needs:
   - specify which host you want to pull the logs from,
   - what files to replicate (you may specify wildcards freely)
   - what destination directory is used to put the logs to.
   - what way of filtered logs need to use.
   - what scripts should be runed when certain events be happened in log files.

4. Create SSH private-public key pair using "ssh-keygen -t rsa".
   Put public key to each of logs source machines using
   "ssh-copy-id remote_user@machine-to-be-pulled" - you should have 
   access to all these machines from the log server without
   entering a password.

5. Now run /etc/init.d/clever_logging start
   After all that steps logs from source machines will be automatically
   and continuosly replicated to the log server in the background (with 
   support of reconnects and resumes if the connection is not stable). 
   You may monitor the activity at standard /var/log/messages file.
   No need to configure source machines - the single configuration
   point is clever_logging.conf at your logging server.


THE PROBLEM WHICH IS SOLVED
---------------------------

If you have many machines in your cluster which perform different tasks 
(e.g. SQL server, web-frontend, balancer, mail server etc.), you may want 
to aggregate logs from all these machines to a single place to monitor them 
or to preform various statistics collections. Of course you may configure 
syslog or syslog-ng to pass all the data over the network, but if you do
so, you are face to face with the following problems:

1. Due to network problems pieces of logs may not be correctly transfered,
   so you may loose data.
2. It is quite hard to keep the syslog configuration in sync with real
   world (which is changing time to time, you know).
3. Not all services support putting logs into syslog (e.g. apache supports
   only file-based logs writing). So you have to use named pipes, and your 
   configs grow.
4. Quite oftenly you want to replicate logs filenames by their wildcards
   specified. Syslog cannot do that.
5. Not all services may send and keep not interesting events in log files 
6. Sometime you want to run certain scripts when certain events is happened in log files

clever_logging solves all that problems.
