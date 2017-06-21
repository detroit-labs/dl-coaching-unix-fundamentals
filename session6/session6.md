%title: Unix Fundamentals Session 6
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 6 <-
---------------

-> A Detroit Labs Developer Coaching Course <-

----------------------------------------------------------------------------

-> The $0 variable <-
=====================

Previously we've seen `$1`, `$2`, and so on, which store the
positional parameters for a shell program. The `$0` variable stores
the name that the program was executed as. Referencing `$0` can
provide decision-making for programs that are linked on the filesystem
under different names.

    session6$ echo $0
    bash
    session6$ cat > namescript
    echo $0
    session6$ chmod +x namescript
    session6$ ./namescript
    ./namescript


----------------------------------------------------------------------------

-> Parameter Substitution <-
============================

The `${...}` is used for parameter substitution. In its most common
use, `${...}` is used for disambiguation. Normally a variable would be
prepended with `$` when referencing it. However, this can cause
ambiguity where a substitution might also be a interfere with the
formation of other valid parameters or commands, which `${...}`
addresses.

    session6$ file=session6.md
    session6$ cp $file $file_backup
    usage: cp source_file target_file
        cp source_file ... target_directory
    session6$ ls
    session6.md
    session6$ cp ${file} ${file}_backup
    session6$ ls
    session6.md		session6.md_backup

----------------------------------------------------------------------------

-> Parameter Substitution <-
============================

The `${...}` construct has additional capabilities though.

\- - `${TERM:-xterm-256color}`
If TERM is null, use xterm-256color instead.

= - `${TERM:=xterm-256color}`
If TERM is null, use xterm-256color, and set TERM to xterm-256color.

+ - `${TERM:+"TERM is set"}`
If TERM is not null, substitute with "TERM is set"

? - `${TERM:?"TERM is not set!"}`
If TERM is null, print "TERM is not set!" to stderr, do nothing else.

\# - `${#TERM}`
The parameter is replaced with the number of characters in its value.

... and pattern matching. Reference your book for the listing.

----------------------------------------------------------------------------

-> The set Command <-
=====================

The `set` command helps manage the environment and active shell
options. By default, `set` simply prints the current environment.

    session6$ set
    ANDROID_HOME=/Users/sleepynate/.android/sdk
    BASH=/bin/bash
    BASH_VERSION='3.2.57(1)-release'
    COLUMNS=138
    HISTFILE=/Users/sleepynate/.bash_history
    HISTFILESIZE=500
    HISTSIZE=500
    HOME=/Users/sleepynate
    HOSTNAME=beast-at-tanagra
    HOSTTYPE=x86_64
    IFS=$' \t\n'
    LANG=en_US.UTF-8
    LC_CTYPE=en_US.UTF-8
    ...

----------------------------------------------------------------------------

-> Trace Mode <-
================

The `set` command is also used to turn on shell options that would
normally be passed when the shell was instantiated. Calling `set -x`
will turn on *trace mode* which prints commands as they are parsed and
executed by the shell.

    session6$ set -x
    session6$ echo "i am echoing things"
    + echo 'i am echoing things'
    i am echoing things
    session6$ ./left
    + ./left
    I am left script!
    session6$ myvar=100
    + myvar=100
    session6$ echo "Keeping it $myvar!"
    echo "Keeping it $myvar"
    + echo 'Keeping it 100'
    Keeping it 100

----------------------------------------------------------------------------

-> Setting Positional Parameters <-
===================================

The `set` command can be used to set positional parameters. Each
argument provided to `set` when there are no other optional will be
assigned to each of the positional parameters respectively.

    session6$ echo $1 $2 $3

    session6$ set i like pizza
    session6$ echo $1 $3
    i pizza
    session6$ echo $2
    like
    session6$

----------------------------------------------------------------------------

-> Other Useful set Modes <-
============================

-a - Automatically export all variables

-c - Don't clobber (overwrite) files

-e - Exit if any command fails

-v - Print every command entered

----------------------------------------------------------------------------

-> The Internal Field Separator <-
==================================

The Internal Field Separator or *IFS* is the character or set of
characters use to separate fields, that is: input parsed from `read`,
or output from command substitution or variable substitution.

    session6$ echo $IFS

    session6$ echo $IFS | od -b
    0000000   012
    0000001


    session6$ IFS=:
    session6$ read a b c
    6:7:8
    session6$ echo $a
    6
    session6$ echo $b
    7



----------------------------------------------------------------------------

-> The readonly Command <-
==========================

The `readonly` command prevents a variable from being changed in the
current shell instance.

    session6$ echo $TERM
    xterm-256color
    session6$ readonly TERM
    session6$ TERM="iTerm2"
    bash: TERM: readonly variable

----------------------------------------------------------------------------

-> The unset Command <-
=======================

The `unset` command deletes variables.

    session6$ echo $ANDROID_HOME
    /Users/sleepynate/.android/sdk
    session6$ unset ANDROID_HOME
    session6$ echo $ANDROID_HOME

----------------------------------------------------------------------------

-> The eval Command <-
======================

The `eval` command evaluates commands. This might seem redundant since
that is the default behavior of the shell. However, `eval` runs the
command with the arguments provided to it after they are already
parsed once to be passed to `eval`. This allows for command evaluating
dynamically generated commands that would not normally be allowed.

    session6$ eval echo I am an eval
    I am an eval
    session6$ pipe="|"
    session6$ echo $IFS $pipe od -b
    | od -b
    session6$ ls -l $pipe wc -l
    ls: -l: No such file or directory
    ls: wc: No such file or directory
    ls: |: No such file or directory
    session6$ eval ls -l $pipe wc -l
        7

----------------------------------------------------------------------------

-> The eval Command <-
======================

    session6$ cat > lastarg
    eval echo \$$#
    session6$ chmod +x lastarg
    session6$ ./lastarg a b c d
    d
    session6$ ./lastarg taco burrito guac
    guac

----------------------------------------------------------------------------

-> The wait Command <-
======================

The `wait` command works like `sleep`, but rather than taking a number
of seconds as an argument, it takes a process ID. `wait` pauses
execution until that process completes, and is used to help continue
working after a long-running background operation.

    session6$ find /usr/local/ -name byobu > filelist &
    [1] 63654
    session6$ date
    Wed Jun 21 11:05:21 EDT 2017
    session6$ wait 63654
    [1]+  Done                    find /usr/local/ -name byobu > filelist
    session6$ date
    Wed Jun 21 11:05:30 EDT 2017


----------------------------------------------------------------------------

-> The $! variable <-
=====================

The `$!` variable stores the pid of the last process that was created
by the shell. This is handy in conjunction with the `wait` command for
capturing the pid of a long running command programmatically.

    session6$ cat timesearch
    #!/usr/bin/env bash

    source="$1"
    search="$2"

    find "$source" -name "$search" > searchfile.tmp &
    pid="$!"
    date
    wait $pid
    date
    cat searchfile.tmp
    rm searchfile.tmp

----------------------------------------------------------------------------

-> The trap Command <-
======================

When commands end or a user cancels a command, the shell sends
*signals* to the operating system to carry out the operation
requested. Within a session, we can capture and react to those signals
using the `trap` command. `trap` takes a string of commands for its
first argument, followed by any number of signals to react to. If any
of the provided signals are intercepted, trap executes the commands
from its first argument.

`trap "commands" [1, 2 ...]`

`0`  - Shell exited
`1`  - User disconnected
`2`  - User interrupted (Ctrl-c)
`15` - SIGTERM (kill command)

To reset a trapped signal, simply call trap without the command list.

`trap 1 2 15`

----------------------------------------------------------------------------

-> The trap Command <-
======================

    session6$ cat timesearch
    #!/usr/bin/env bash

    source="$1"
    search="$2"

    find "$source" -name "$search" > searchfile.tmp &
    pid="$!"
    date
    trap "echo 'User cancelled. Cleaning up.'; kill $pid" 2
    wait $pid
    date
    [ -f searchfile.tmp ] && cat searchfile.tmp
    rm searchfile.tmp
