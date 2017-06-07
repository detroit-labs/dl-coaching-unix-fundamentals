%title: Unix Fundamentals Session 5
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 5 <-
---------------

-> A Detroit Labs Developer Coaching Course <-

----------------------------------------------------------------------------

-> The read Command <-
======================

The `read` command takes one or more arguments and then reads from
standard input. Data provided to `read` from standard input are
considered whitespace-delimited and are assigned to variables in the
current environment using the symbols provided as arguments
respectively.

This is often used to interact with a user while a program is running.

    session5$ read a
    10
    session5$ echo $a
    10
    session5$ read x y
    3 6 9
    session5$ echo $x
    3
    session5$ echo $y
    6 9

----------------------------------------------------------------------------

-> The read Command <-
======================

    session5$ cat safecp
    #!/usr/bin/env bash
    if [ ! $# -eq 2 ]
    then
        echo "Usage: safecp source destination"
        exit 1
    fi
    source=$1
    dest=$2
    if [ -e $dest ]
    then
        echo "$dest exists. Overwrite? (y/n) \c"
        read answer
        if [ "$answer" != "y" ]
        then
            echo "Copy aborted."
            exit 0
        fi
    fi
    cp $source $dest

----------------------------------------------------------------------------

-> The read Command <-
======================

    session5$ ./safecp ../session4/online myscript
    session5$ ./safecp ../session4/online myscript
    myscript exists. Overwrite? (y/n) y
    session5$ ./safecp ../session4/online myscript
    myscript exists. Overwrite? (y/n) n
    Copy aborted.

----------------------------------------------------------------------------

-> Extended echo Characters <-
==============================

\\0nnn - ascii character represented by an octal number
\\b    - backspace
\\t    - tab
\\c    - no line ending
\\f    - form feed
\\n    - new line
\\r    - carriage return

----------------------------------------------------------------------------

-> Extended echo Characters <-
==============================

    session5$ echo -e "nathan \f paul"
    nathan
        paul
    session5$ echo -e "nathan \c paul"
    nathan session5$ echo -e "nathan \b\b\b\b paul"
    d paul
    session5$ echo -e "nathan \055 paul"
    nathan - paul

----------------------------------------------------------------------------

-> The safercp Script <-
========================

Let's take a look at the `safercp` script included with this lesson to
get an idea of what a well-rounded interactive program might look like.

----------------------------------------------------------------------------

    session5$ for i in a b c d e; do echo "$i" > $i; done; mkdir mydir
    session5$ ls
    a         c         e         safecp     session5.md
    b         d         mydir     safercp
    session5$ ./safercp a f
    session5$ cat f
    a
    session5$ ./safercp b d
    d already exists. Overwrite? (y/n) n
    session5$ cat d
    d
    session5$ ./safercp b d
    d already exists. Overwrite? (y/n) y
    session5$ cat d
    b
    session5$ ./safercp a mydir/
    session5$ ./safercp b c mydir/
    session5$ ls mydir/
    a       b       c
    session5$ ./safercp d e f
    Usage: safercp file1 file2
           safercp file[s] dir

----------------------------------------------------------------------------

-> The contacts Script <-
=========================

Let's also take a look at the `contacts` script included with this
lesson to better understand how robust interactive shell programs are
built by combining utility commands with interactive data input.

----------------------------------------------------------------------------

-> The $$ Variable <-
=====================

The `$$` special variable holds the PID of the login shell for the
current session. This is useful when writing temporary files to
disambiguate between multiple users using the same program
concurrently.

       grep -v "$name" phonebook > /tmp/phonebook$$
       mv /tmp/phonebook$$ phonebook

----------------------------------------------------------------------------

-> Exit Status of read <-
=========================

The exit status of `read` is always 0, unless it receives an EOF
(end-of-file) from standard input, or the user pressing Ctrl+D while
reading interactively from the shell. Thus, `read` can be used as a
test condition for reading structured data into loops or if
statements.

    session5$ echo -e "1 2\n3 4\n5 6" |
    > while read a b
    > do
    >   echo $((a + b))
    > done
    3
    7
    11

----------------------------------------------------------------------------

-> Exit Status of read <-
=========================

    session5$ while read first last number
    > do
    >   echo "$first $last's phone number is $number"
    > done < phonebook
    Albert Chiba's phone number is 973-555-2051
    Brenda Sonars's phone number is 201-555-5972
    Lefty Sanchez's phone number is 221-555-9282
    Steve Govad's phone number is 210-555-7677
    Tonya Isotalo's phone number is 739-555-9152

----------------------------------------------------------------------------

-> The printf Command <-
========================

Tools like `cut`, `paste`, `grep` and `sed` are great for organizing
data, but the powerhouse for modifying data's format for a different
interpretation lies in `printf`.

    session5$ printf "The hexadecimal value for %d is %x\ n" 30 30
    The hexadecimal value for 30 is 1e

    session5$ printf "The unsigned value for %d is %u\ n" –1000 –1000
    The unsigned value for -1000 is 4294966296

    session5$ printf "%50s\n" "Indented to 50 characters"
                            Indented to 50 characters

    session5$ printf "%s\t%s\n" "1" "2 3" "4" "5"
    1       2 3
    4       5

    session5$ printf "%.1f\n" 255 3.5
    255.0
    3.5

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
