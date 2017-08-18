Session 4 Group Exercise
========================

In this directory, you will find a file named `secure.log`, which is
normally located at `/var/log/secure` on Linux systems and collects
log entries for security risks such as failed logins or attempted
access from unauthorized hosts. This file is from a Linux system
undergoing a brute-force attack in an attempt to get unauthorized
access to the system.

As Systems Administrators, you have been tasked with finding out which
users the attackers are attempting to log in as, from what IP
addresses, and how frequently. As a group, work together to write a
single command to show the most frequent failed login attempts grouped
by username and IP address. The output should be of the following
format, with the most frequently failed login attempts first.

       4 illegal user bob ::ffff:26.122.60.65
       3 illegal user jane ::ffff:75.86.254.39
       2 root ::ffff:121.69.99.3
       1 root ::ffff:201.96.254.142
