%title: Unix Fundamentals Session 8
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 8 <-
---------------

-> A Detroit Labs Developer Coaching Course <-

----------------------------------------------------------------------------

-> Local Variables and the Environment <-
=========================================

Variables are only visible in the environment that they are set
in. By default, most commands will not be able to see variables that
are not evaluated on the command line before being passed as
arguments.

    session5$ echo 'echo "The value of variable x is $x"' > nope
    session5$ x=100
    session5$ echo $x
    100
    session5$ ./nope
    The value of variable x is

    session5$ cat > nope2
    x="cookies"
    echo "The value of variable x is $x"
    session5$ chmod +x nope2
    session5$ ./nope2
    The value of variable x is cookies
    session5$ echo $x
    100

----------------------------------------------------------------------------

-> Subshells <-
===============

A *subshell* is a effectively an entirely new session of the shell
launched by your login shell to run programs. By default, shell
programs run in a subshell, so they maintain their own
environment. The environment of a subshell is independent of its
*parent* shell, so variables local to the parent shell are
inaccessible to the subshell.



|¯¯¯¯¯¯¯¯¯¯¯¯¯|
| login shell |------------> x=100
|_____________|
        |
        V
|¯¯¯¯¯¯¯¯¯¯¯¯¯|
|  sub shell  |------------> x=cookies
|_____________|

----------------------------------------------------------------------------

-> Exported Variables <-
========================

The `export` command makes variables visible to subshells launched
from the current environment. However, any changes to the value of
variables in a subshell are only relevant to the subshell. Variables
are essentially copied into the new shell instance, but any assignment
in a subshell van be considered a new local variable.

    session5$ export x
    session5$ ./nope
    The value of variable x is 100
    session5$ ./nope2
    The value of variable x is cookies
    session5$ ./nope
    The value of variable x is 100

----------------------------------------------------------------------------

-> Passing Variables <-
=======================

Rather than exporting variables to all subshells, variables can be set
for specific commands when they're executed. This is done by
prepending the command with a list of variables to set for the
environment the subshell will run in. Variables passed in this way
need not even be set as variables in the current environment.

    session5$ echo $x
    100
    session5$ x=9001 ./nope
    The value of variable x is 9001
    session5$ unset x
    session5$ echo $x

    session5$ x=9002 ./nope
    The value of variable x is 9002

----------------------------------------------------------------------------

-> The source Command <-
========================

To write a program that makes changes to the current environment, one
would need to invoke the *source* command. This is a shell built-in
represented by `.`, though most modern shells alias this to `source`
for convenience. Running a script via `source` runs the commands in
the script in the current environment, not in a subshell. Thus, any
changes made to variables in the script will effect the environment
that remains when the script finishes.

    session5$ . nope
    The value of variable x is 100
    session5$ . nope2
    The value of variable x is cookies
    session5$ echo $x
    cookies

----------------------------------------------------------------------------

-> The Environment <-
=====================

Having mentioned the environment no less than 11 times already, it's
perhaps pertinent to understand what it is and where it comes
from. The *environment* is the set of variables that tell the shell
where to find all of the commands, files and other values that it
needs to perform its task of allowing you to interface with the
operating system. The `env` command, when called with no arguments,
will print a list of the environment.

    session5$ env
    TERM_PROGRAM=iTerm.app
    NVM_CD_FLAGS=-q
    ANDROID_HOME=/Users/sleepynate/.android/sdk
    TERM=xterm-256color
    SHELL=/bin/zsh
    NVM_PATH=/Users/sleepynate/.nvm/versions/node/v6.9.1/lib/node
    TERM_PROGRAM_VERSION=3.0.15
    USER=sleepynate
    NVM_DIR=/Users/sleepynate/.nvm
    PAGER=less
    ...

----------------------------------------------------------------------------

-> Profile & rc files <-
========================

When a new shell is instantiated, it sources a series of files. Some
of these are stored in the system configuration folder `/etc`, and
provide values relevant to working with the operating system in
general. Others are set for specific users. When a user logs into a
system, a shell usually sources `~/.profile`, and if it is a bash
shell, `~/.bash_profile`. Additionally, shells may read an *rc* file
like `~/.bashrc` to set further configure the shell. These files
contain values like `$HOME`, `$PS1` and `$PATH`


----------------------------------------------------------------------------

-> Common environment variables <-
==================================

`$HOME` stores the location of the user's home directory
`$PS1` stores the format string for the prompt
`$PATH` stores a colon-delimited list of where to find commands
`$CDPATH` stores a list of places to look for relative paths
`$TERM` stores the terminal in use
`$TZ` stores the user's timezone

----------------------------------------------------------------------------

-> The $0 variable <-
=====================

Previously we've seen `$1`, `$2`, and so on, which store the
positional parameters for a shell program. The `$0` variable stores
the name that the program was executed as. Referencing `$0` can
provide decision-making for programs that are linked on the filesystem
under different names.

    session8$ echo $0
    bash
    session8$ cat > namescript
    echo $0
    session8$ chmod +x namescript
    session8$ ./namescript
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

    session8$ file=session8.md
    session8$ cp $file $file_backup
    usage: cp source_file target_file
        cp source_file ... target_directory
    session8$ ls
    session8.md
    session8$ cp ${file} ${file}_backup
    session8$ ls
    session8.md		session8.md_backup

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

    session8$ set
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

    session8$ set -x
    session8$ echo "i am echoing things"
    + echo 'i am echoing things'
    i am echoing things
    session8$ ./left
    + ./left
    I am left script!
    session8$ myvar=100
    + myvar=100
    session8$ echo "Keeping it $myvar!"
    echo "Keeping it $myvar"
    + echo 'Keeping it 100'
    Keeping it 100

----------------------------------------------------------------------------

-> Setting Positional Parameters <-
===================================

The `set` command can be used to set positional parameters. Each
argument provided to `set` when there are no other optional will be
assigned to each of the positional parameters respectively.

    session8$ echo $1 $2 $3

    session8$ set i like pizza
    session8$ echo $1 $3
    i pizza
    session8$ echo $2
    like
    session8$

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

    session8$ echo $IFS

    session8$ echo $IFS | od -b
    0000000   012
    0000001


    session8$ IFS=:
    session8$ read a b c
    6:7:8
    session8$ echo $a
    6
    session8$ echo $b
    7



----------------------------------------------------------------------------

-> The readonly Command <-
==========================

The `readonly` command prevents a variable from being changed in the
current shell instance.

    session8$ echo $TERM
    xterm-256color
    session8$ readonly TERM
    session8$ TERM="iTerm2"
    bash: TERM: readonly variable

----------------------------------------------------------------------------

-> The unset Command <-
=======================

The `unset` command deletes variables.

    session8$ echo $ANDROID_HOME
    /Users/sleepynate/.android/sdk
    session8$ unset ANDROID_HOME
    session8$ echo $ANDROID_HOME
