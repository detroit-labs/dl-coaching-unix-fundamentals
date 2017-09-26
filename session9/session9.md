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
parsed once to be passed to `eval`. This allows for evaluating
dynamically generated commands that would not normally be allowed.

    session9$ eval echo I am an eval
    I am an eval
    session9$ pipe="|"
    session9$ echo $IFS $pipe od -b
    | od -b
    session9$ ls -l $pipe wc -l
    ls: -l: No such file or directory
    ls: wc: No such file or directory
    ls: |: No such file or directory

    session9$ eval ls -l $pipe wc -l
        7

----------------------------------------------------------------------------

-> The eval Command <-
======================

We can use `eval`'s post-parsing evaluation context to dynamically
construct variable names.

    session9$ cat > lastarg
    eval echo \$$#
    session9$ chmod +x lastarg
    session9$ ./lastarg a b c d
    d
    session9$ ./lastarg taco burrito guac
    guac

----------------------------------------------------------------------------

-> The eval Command <-
======================

This same context can be used to store variable names as strings for
dereferencing later.

    session9$ foo="💯"
    session9$ bar=foo
    session9$ eval "echo $bar"
    foo
    session9$ eval echo $bar
    foo
    session9$ eval echo \$bar
    foo
    session9$ eval echo \$$bar
    💯

----------------------------------------------------------------------------

-> The eval Command <-
======================

~~~
session9 $ ssh-agent
SSH_AGENT_PID=27504; export SSH_AGENT_PID;
echo Agent pid 27504;
session9 $ echo $SSH_AGENT_PID

session9 $ eval $(ssh-agent)
Agent pid 29469
session9 $ echo $SSH_AGENT_PID
29469
~~~

----------------------------------------------------------------------------

-> The wait Command <-
======================

The `wait` command works like `sleep`, but rather than taking a number
of seconds as an argument, it takes a process ID. `wait` pauses
execution until that process completes, and is used to help continue
working after a long-running background operation.

    session9$ find /usr/local/ -name byobu > filelist &
    [1] 63654
    session9$ date
    Wed Jun 21 11:05:21 EDT 2017
    session9$ wait 63654
    [1]+  Done                    find /usr/local/ -name byobu > filelist
    session9$ date
    Wed Jun 21 11:05:30 EDT 2017


----------------------------------------------------------------------------

-> The $! variable <-
=====================

The `$!` variable stores the pid of the last process that was created
by the shell. This is handy in conjunction with the `wait` command for
capturing the pid of a long running command programmatically.

~~~
session9$ cat timesearch
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
~~~

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

~~~
session9$ cat timesearch
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
~~~

----------------------------------------------------------------------------

-> bash functions <-
====================

The bash shell (among other POSIX-standard shells) gives us the
ability to define *functions*. Functions work much the same way as
shell programs, however they are defined only in the context of the
current environment. This means they can be altered or unset just as
variables can. They are defined with the following format:

    funcName () { command1; command2; commandN; }

----------------------------------------------------------------------------

-> bash functions <-
====================

Much like shell scripts, functions can take arguments which are bound
as with the `set command`;

    session9$ say3times () { echo $1; echo $1; echo $1; }
    session9$ say3times hello
    hello
    hello
    hello

----------------------------------------------------------------------------

-> bash functions <-
====================

Be sure that if you want to capture the exit status of a function, you
use `return` instead of `exit`. `return` will set the exit status of
the function as it finishes execution (the default is 0). Accidentally
using `exit` will cause the shell in which the function is executing
to exit.

----------------------------------------------------------------------------

-> The type command <-
======================

The type command can be used to determine where a command or function
is executed from.

    session9$ type echo
    echo is a shell builtin
    session9$ type less
    less is /usr/bin/less
    session9$ type say3times
    say3times is a function
    say3times ()
    {
        echo $1;
        echo $1;
        echo $1
    }
