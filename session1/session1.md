%title: Unix Fundamentals Session 1
%author: Nathan Dotz
%date: 2017-05-05










-> Unix Fundamentals <-
=======================

-> Session 1 <-
------------------

-> A Detroit Labs Developer Coaching Course <-

--------------------------------------------------------------------------------

-> What's a Unix anyways? <-
============================

*_Unix_* describes a family of operating systems and international
standard deriving from the AT&T Unix operating system developed at
Bell Labs in 1970s

--------------------------------------------------------------------------------

-> Features of Unix <-
======================

* portable
* multi-tasking
* time-sharing multi-user

--------------------------------------------------------------------------------

-> The Unix Philosophy <-
=========================

* plain-text
* heirarchical file system
* devices, IPC, everything are files
* user environment is programming environment
* many small programs which interact

> "the idea that the power of a system comes more from the
> relationships among programs than from the programs themselves"
>
> The Unix Programming Environment, Kernighan & Pike

--------------------------------------------------------------------------------

-> Unix History <-
==================

* Bell Labs, 1960s
* Ken Thompson & Dennis Ritchie
* Ported to C in 1972
* BSD 1977-1995
* System V
* GNU Project, 1983
* Linus Torvalds releases Linux, 1991
* SCO sues everyone
* FreeBSD/NetBSD/OpenBSD
* MacOS, iOS, Android

--------------------------------------------------------------------------------

-> What is an Operating System? <-
==================================

> An *_Operating System_* is *system software* that manages computer
> hardware and software resources to provide common services for
> computer programs.
> - Wikipedia

* Kernel
* System Call Library
* System Utilities
* Shell
* User Applications

--------------------------------------------------------------------------------

-> The Unix Shell <-
====================

* Command-line interpreter
* Text access to applications
* Terminal Emulator
* Allows Scripting
* Programmable

--------------------------------------------------------------------------------

-> First commands <-
====================

    beast-at-tanagra:~ sleepynate$ date
    Mon May  8 23:55:57 EDT 2017

    beast-at-tanagra:~ sleepynate$ who
    sleepynate console  May  2 17:48
    sleepynate ttys000  May  2 17:48
    sleepynate ttys003  May  7 23:46

    beast-at-tanagra:~ sleepynate$ who am i
    sleepynate ttys005  May  8 23:59

    beast-at-tanagra:~ sleepynate$ echo "hello world"
    hello world

--------------------------------------------------------------------------------

-> The Unix File System <-
==========================

* files
* directories
* device files
* sockets/fifo
* symlinks

--------------------------------------------------------------------------------

-> Standard Directory Layout <-
===============================

/        root of the filesystem
/bin     *binaries* fundamental to the system
/boot    stuff for booting the system
/dev     device files
/etc     system configuration files
/home    user home directories
/lib     C headers & libraries
/mnt     mounting point for file system devices
/root    home directory of the root user
/sbin    *system binaries* essential to systems administration
/tmp     temporary file area that is purged regularly
/usr     holds *user* programs and libraries that aren't system critical
/var     a place for logs, databases, system mail

[Another Chart on Wikipedia shows better structure](https://upload.wikimedia.org/wikipedia/commons/f/f3/Standard-unix-filesystem-hierarchy.svg)

--------------------------------------------------------------------------------

-> What's in my home directory? <-
==================================

    beast-at-tanagra:~ sleepynate$ ls
    Applications           Music
    Audiobooks             Pictures
    BattleScribe           Public
    Desktop                Refridgerator
    Documents              Samples
    Downloads              VirtualBox VMs
    Dropbox                asset_copy.sh
    Dropbox (Detroit Labs) big_dickbutt.jpg
    Dropbox (Personal)     code
    Games                  good job.png
    Library                todo.org
    Movies                 todo.org_archive

--------------------------------------------------------------------------------

-> How do we get around? <-
===========================

    beast-at-tanagra:~ sleepynate$ pwd
    /Users/sleepynate
    beast-at-tanagra:~ sleepynate$ cd code/
    beast-at-tanagra:code sleepynate$ pwd
    /Users/sleepynate/code
    beast-at-tanagra:code sleepynate$ cd dl-coaching
    beast-at-tanagra:dl-coaching sleepynate$ cd unix-fundamentals/session1/
    beast-at-tanagra:session1 sleepynate$ ls
    dir1		myprogram	names
    beast-at-tanagra:session1 sleepynate$ pwd
    /Users/sleepynate/code/dl-coaching/unix-fundamentals/session1

--------------------------------------------------------------------------------

-> How do we get around? <-
===========================

    beast-at-tanagra:session1 sleepynate$ cd ..
    beast-at-tanagra:unix-fundamentals sleepynate$ pwd
    /Users/sleepynate/code/dl-coaching/unix-fundamentals
    beast-at-tanagra:unix-fundamentals sleepynate$ cd session1
    beast-at-tanagra:session1 sleepynate$ cd ../..
    beast-at-tanagra:dl-coaching sleepynate$ pwd
    /Users/sleepynate/code/dl-coaching

--------------------------------------------------------------------------------

-> What's in a file? <-
=======================

    beast-at-tanagra:~ sleepynate$ cat names
    Ken Thompson
    Dennis Ritchie
    John McCarthy
    Simon Peyton Jones
    Robin Milner

--------------------------------------------------------------------------------

-> Command Options <-
====================

    beast-at-tanagra:~ sleepynate$ wc names
        5      11      74 names

    beast-at-tanagra:~ sleepynate$ wc -l names
        5 names

    beast-at-tanagra:~ sleepynate$ wc -c names
        74 names

    beast-at-tanagra:~ sleepynate$ wc -w names
        11 names

--------------------------------------------------------------------------------

-> Managing files <-
====================

    beast-at-tanagra:dl-coaching sleepynate$ cd unix-fundamentals/session1
    beast-at-tanagra:session1 sleepynate$ ls
    dir1		myprogram	names
    beast-at-tanagra:session1 sleepynate$ cp names saved_names
    beast-at-tanagra:session1 sleepynate$ ls
    dir1		myprogram	names		saved_names

    beast-at-tanagra:session1 sleepynate$ mv saved_names hold_it
    beast-at-tanagra:session1 sleepynate$ ls
    dir1		hold_it		myprogram	names

    beast-at-tanagra:session1 sleepynate$ cp names old_names
    beast-at-tanagra:session1 sleepynate$ ls
    dir1		hold_it		myprogram	names		old_names
    beast-at-tanagra:session1 sleepynate$ rm hold_it old_names
    beast-at-tanagra:session1 sleepynate$ ls
    dir1		myprogram	names

--------------------------------------------------------------------------------

-> Managing files <-
====================

    beast-at-tanagra:session1 sleepynate$ ls -l
    total 16
    drwxr-xr-x  6 sleepynate  staff  204 May  9 23:05 dir1
    -rwxr-xr-x  1 sleepynate  staff  114 May  9 23:20 myprogram
    -rw-r--r--  1 sleepynate  staff   74 May  9 16:02 names

total line shows number of storage blocks
first column is directory(d), file(-), or
columns 2-4 show owner read(r), write(w), execute(x) permissions, or not(-)
columns 5-7 show group read(r), write(w), execute(x) permissions, or not(-)
columns 8-10 are global read(r), write(w), execute(x) permissions, or not(-)
next column is link count
owner's name
owning group's name
size in bytes
date modified
file name

--------------------------------------------------------------------------------

-> Managing files <-
====================

    beast-at-tanagra:session1 sleepynate$ ls -l dir1
    total 16
    drwxr-xr-x  5 sleepynate  staff  170 May  9 23:08 dir2
    drwxr-xr-x  4 sleepynate  staff  136 May  9 23:08 dir3
    -rw-r--r--  1 sleepynate  staff   11 May  9 23:05 file1
    -rw-r--r--  1 sleepynate  staff   11 May  9 23:05 file2

    beast-at-tanagra:session1 sleepynate$ ls -l dir1/dir2/
    total 24
    -rw-r--r--  1 sleepynate  staff  11 May  9 23:07 file3
    -rw-r--r--  1 sleepynate  staff  16 May  9 23:07 file5
    -rw-r--r--  1 sleepynate  staff  23 May  9 23:08 file7

--------------------------------------------------------------------------------

-> Linking Files <-
===================

    beast-at-tanagra:session1 sleepynate$ ln myprogram mylinkedprogram
    beast-at-tanagra:session1 sleepynate$ ls -l
    total 24
    drwxr-xr-x  6 sleepynate  staff  204 May  9 23:05 dir1
    -rwxr-xr-x  2 sleepynate  staff  114 May  9 23:20 mylinkedprogram
    -rwxr-xr-x  2 sleepynate  staff  114 May  9 23:20 myprogram
    -rw-r--r--  1 sleepynate  staff   74 May  9 16:02 names

--------------------------------------------------------------------------------

-> Linking Files <-
===================

    beast-at-tanagra:session1 sleepynate$ ln -s mylinkedprogram symprogram
    beast-at-tanagra:session1 sleepynate$ ls -l
    total 32
    drwxr-xr-x  6 sleepynate  staff  204 May  9 23:05 dir1
    -rwxr-xr-x  2 sleepynate  staff  114 May  9 23:20 mylinkedprogram
    -rwxr-xr-x  2 sleepynate  staff  114 May  9 23:20 myprogram
    -rw-r--r--  1 sleepynate  staff   74 May  9 16:02 names
    lrwxr-xr-x  1 sleepynate  staff   15 May 10 17:10 symprogram -> mylinkedprogram

--------------------------------------------------------------------------------

-> Linking Files <-
===================

    beast-at-tanagra:session1 sleepynate$ rm mylinkedprogram
    beast-at-tanagra:session1 sleepynate$ ls -l
    total 24
    drwxr-xr-x  6 sleepynate  staff  204 May  9 23:05 dir1
    -rwxr-xr-x  1 sleepynate  staff  114 May  9 23:20 myprogram
    -rw-r--r--  1 sleepynate  staff   74 May  9 16:02 names
    lrwxr-xr-x  1 sleepynate  staff   15 May 10 17:10 symprogram -> mylinkedprogram
    beast-at-tanagra:session1 sleepynate$ ./symprogram
    bash: ./symprogram: No such file or directory

`symprogram` is now a *dangling symbolic link*.

--------------------------------------------------------------------------------

-> Creating and Removing Directories <-
=======================================

    beast-at-tanagra:session1 sleepynate$ mkdir tmpdir
    beast-at-tanagra:unix-fundamentals sleepynate$ mkdir -p tmpdir/tmpdir2/tmpdir3
    beast-at-tanagra:unix-fundamentals sleepynate$ ls tmpdir/tmpdir2/
    tmpdir3
    beast-at-tanagra:session1 sleepynate$ rmdir tmpdir
    rmdir: tmpdir: Directory not empty
    beast-at-tanagra:session1 sleepynate$ rmdir tmpdir/tmpdir2/
    rmdir: tmpdir/tmpdir2/: Directory not empty
    beast-at-tanagra:session1 sleepynate$ rm tmpdir/tmpdir2/tmpdir3/
    rm: tmpdir/tmpdir2/tmpdir3/: is a directory
    beast-at-tanagra:session1 sleepynate$ rmdir tmpdir/tmpdir2/tmpdir3/
    beast-at-tanagra:session1 sleepynate$ ls tmpdir/tmpdir2/
    beast-at-tanagra:session1 sleepynate$ rm -r tmpdir/
    beast-at-tanagra:session1 sleepynate$ ls
    dir1		myprogram	names

--------------------------------------------------------------------------------

-> Globbing <-
==============

    beast-at-tanagra:session1 sleepynate$ cd dir1/
    beast-at-tanagra:dir1 sleepynate$ ls
    dir2	dir3	file1	file2
    beast-at-tanagra:dir1 sleepynate$ cat file1 file2
    I am file1
    I am file2
    beast-at-tanagra:dir1 sleepynate$ cat file2 file1
    I am file2
    I am file1
    beast-at-tanagra:dir1 sleepynate$ cat *
    cat: dir2: Is a directory
    cat: dir3: Is a directory
    I am file1
    I am file2
    beast-at-tanagra:dir1 sleepynate$ echo *
    dir2 dir3 file1 file2
    beast-at-tanagra:dir1 sleepynate$ echo * : *
    dir2 dir3 file1 file2 : dir2 dir3 file1 file2

--------------------------------------------------------------------------------

-> Globbing <-
==============

    beast-at-tanagra:dir1 sleepynate$ ls f*
    file1	file2
    beast-at-tanagra:dir1 sleepynate$ ls *2
    file2

    dir2:
    file3	file5	file7
    beast-at-tanagra:dir1 sleepynate$ ls *ir*
    dir2:
    file3	file5	file7

    dir3:
    file4	file8
    beast-at-tanagra:dir1 sleepynate$ ls -d *ir*
    dir2	dir3

--------------------------------------------------------------------------------

-> Globbing <-
==============

    beast-at-tanagra:dir1 sleepynate$ echo [df]i*2
    dir2 file2

    beast-at-tanagra:dir1 sleepynate$ echo [d-f]i*2
    dir2 file2

    beast-at-tanagra:dir1 sleepynate$ echo *[!2]
    dir3 file1

--------------------------------------------------------------------------------

-> I/O Redirection <-
=======================

- Commands normally read from the *standard input* device
- The output of your commands goes to the *standard output* device
- If a command fails, you may see output from the *standard error* device

These are referred to as `stdin`, `stdout` and `stderr`

    beast-at-tanagra:dir1 sleepynate$ wc
    This is some text
    that I am
    typing into
    standard input
    ^D
        4      11      55

--------------------------------------------------------------------------------

-> Output Redirection <-
=======================

    beast-at-tanagra:dir1 sleepynate$ who > users
    beast-at-tanagra:dir1 sleepynate$ cat users
    sleepynate console  May  2 17:48
    sleepynate ttys000  May  2 17:48
    sleepynate ttys003  May  7 23:46
    sleepynate ttys005  May  9 00:09

--------------------------------------------------------------------------------

-> Output Redirection <-
=======================

    beast-at-tanagra:dir1 sleepynate$ echo  "cooluser   ttys999  May 99 99:99" >> users
    beast-at-tanagra:dir1 sleepynate$ cat users
    sleepynate console  May  2 17:48
    sleepynate ttys000  May  2 17:48
    sleepynate ttys003  May  7 23:46
    sleepynate ttys005  May  9 00:09
    cooluser   ttys999  May 99 99:99

But be careful, non-appending redirection will clobber your files.

    beast-at-tanagra:dir1 sleepynate$ echo "nobody" > users
    beast-at-tanagra:dir1 sleepynate$ cat users
    nobody

--------------------------------------------------------------------------------

-> Input Redirection <-
=======================

    beast-at-tanagra:dir1 sleepynate$ cat file1
    I am file1
    beast-at-tanagra:dir1 sleepynate$ wc < file1
        1       3      11

--------------------------------------------------------------------------------

-> Piping <-
============

Because commands generally read from `stdin` and write to `stdout`, of
course there is a redirection mechanism to link the `stdout` of one
command to the `stdin` of the next, the pipe.

    beast-at-tanagra:dir1 sleepynate$ who > users
    beast-at-tanagra:dir1 sleepynate$ wc < users
        4      20     136
    beast-at-tanagra:dir1 sleepynate$ who | wc
        4      20     136

--------------------------------------------------------------------------------

-> The Error Output Stream <-
=============================

    beast-at-tanagra:dir1 sleepynate$ cat *
    cat: dir2: Is a directory
    cat: dir3: Is a directory
    I am file1
    I am file2

    beast-at-tanagra:dir1 sleepynate$ cat * 2> errors
    I am file1
    I am file2
    beast-at-tanagra:dir1 sleepynate$ cat errors
    cat: dir2: Is a directory
    cat: dir3: Is a directory

--------------------------------------------------------------------------------

-> Background Processes <-
==========================

Most commands run in the *foreground* and take over the terminal until
they complete. However, commands can be run in the *background*,
allowing the user to continue to work and enter commands

    beast-at-tanagra:dir1 sleepynate$ emacsclient -nw file1 &
    [1] 8578
    beast-at-tanagra:dir1 sleepynate$ ps
    PID TTY           TIME CMD
    1284 ttys005    0:00.03 /Applications/iTerm.app/Contents/MacOS/iTerm2
    1457 ttys005    0:00.38 -zsh
    5874 ttys005    0:00.28 bash --login
    8578 ttys005    0:00.00 emacsclient -nw file1
    beast-at-tanagra:dir1 sleepynate$ fg
    emacsclient -nw file1

---------------------------------------------------------------------------

-> Loading Programs <-
======================

The *kernel* is the brain of the Unix system. At boot time, the kernel
is loaded from the disk into *memory* where it resides, convincing the
CPU to process thing on behalf of the Unix system and its users.

Aside from a few services and daemons launched and left running at
after the system loads, the rest of the operating system's *programs*
are stored on *disk*, and only get loaded into memory as needed.

The *shell* is one such program, and to get good at shell programming,
you're going to need to know how the system uses it for you.

----------------------------------------------------------------------------

-> Loading Programs <-
======================

- A connection to the system is created physically or via the network
- `init` launches `getty` which reserves a tty device for the
  connection
- `getty` negotiates the start of the authentication process and
  collects the username, passing it to `login` before exiting
- `login` verifies the credentials with `/etc/passwd` and
  `/etc/shadow`
- `login` either rejects the user or launches the user's `shell`
  before exiting

Most ttys these days are *pseudo tty*'s, as classic hardware terminals
that physically connect to Unix machines become exceedingly
rare. These used to represent actual physical devices on the Unix
system.

(init here is a general name, but may specifically be `rc`, `init`,
`launchd`, `systemd`, `upstart` or others.)

----------------------------------------------------------------------------

-> Command execution process <-
===============================

1. shell displays command prompt
2. user enters command
3. shell parses command to decide what to do
4. shell finds appropriate program on disk and asks kernel to load it
5. kernel copies program into memory, begins execution as a *process*
6. shell (usually) turns over `stdin` and `stdout` to the process
7. the process terminates, relinquishing control back to the shell,
   and goes back to the first step

----------------------------------------------------------------------------

-> Command parsing <-
=====================

      mv     myfile    myfile.bak
      ↑         ↑         ↑
      |         |         |
    command--argument--argument

     echo   when do   we   eat
      ↑       ↑   ↑    ↑    ↑
      |       |   |    |    |
    command---+--arguments--+

Some commands, like `setenv` and `logout` are built-in to the
shell. The shell does not need to have the kernel load these into
memory, and can execute them directly. Otherwise, their execution
model is effectively the same.

----------------------------------------------------------------------------

-> Glob parsing <-
==================

    echo *
      |
      | Glob expands
      |
      ↓
    echo  dir1 dir2 file1 file2
      ↑     ↑   ↑    ↑    ↑
      |     |   |    |    |
      |     |   |    |    |
    command-+--arguments--+

Globs are first expanded into their arguments *before* being passed to
commands. In this case, `echo` is passed 4 arguments, not one.

----------------------------------------------------------------------------

-> Redirection parsing <-
=========================

    echo Do unix class homework > reminder
      ↑   ↑   ↑     ↑    ↑      ↑       ↑
      |    \  |     |    |      |       |
      |     | |     |    |      |       |
    command-+--arguments-+  operator-argument

Here, the shell parses the command line and recognizes the special
redirection character. It then knows to split the command into an
`echo` command with 4 arguments, and then the redirection operator
with a single argument. The shell then opens a file stream and
connects `stdout` out to it before calling `echo` with its arguments.
