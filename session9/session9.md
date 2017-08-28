%title: Unix Fundamentals Session 9
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 9 <-
---------------

-> A Detroit Labs Developer Coaching Course <-

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
