%title: Unix Fundamentals Session 6
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 6 <-
---------------

-> A Detroit Labs Developer Coaching Course <-

----------------------------------------------------------------------------

-> The for Construct <-
=======================

The `for` construct provides iteration over multiple items. It takes
each of its arguments and binds it to a local variable for use in its
block of commands. The block of commands is then run for each of the
arguments provided to the `for` construct.

for *var* in *word* *word* *word*
do
  *command*
  *command*
  *command*
done

----------------------------------------------------------------------------

-> The for Construct <-
=======================

    session6$ for i in 2 3 5 8
    > do
    >   echo $i
    > done
    2
    3
    5
    8

----------------------------------------------------------------------------

-> $* revisited <-
==================

If you'll remember from session 5, `$*` captures the entire argument
list as a string. This is probably the first thing we will think of if
we want to process a list of arguments to a shell script we're writing.

    session6$ cat run1
    echo "running $# files"
    for file in $*
    do
        echo "running $file"
        #do something
    done

----------------------------------------------------------------------------

-> $* revisited <-
==================

    session6$ ./run1 file1 file2 file3
    running 3 files
    running file1
    running file2
    running file3

----------------------------------------------------------------------------

-> $* revisited <-
==================

However, `$*` is vulnerable to quoting issues. We lose a set of quotes
each time we evaluate a string, so in some cases, `$*` and `$#` behave
differently.

    session6$ ./run1 'file1 file2' file3
    running 2 files
    running file1
    running file2
    running file3

----------------------------------------------------------------------------

-> $* revisited <-
==================

We could try the old trick of adding a set of quotes which we have been
accustomed to up to this point.

    session6$ cat run2
    echo "running $# files"
    for file in "$*"
    do
        echo "running $file"
        #do something
    done

----------------------------------------------------------------------------

-> $* revisited <-
==================

Unfortunately, adding a set of quotes is unhelpful here, as it simply
processes the entire list as a string of its own.

    session6$ ./run2 file1 file2 file3
    running 3 files
    running file1 file2 file3
    session6$ ./run2 'file1 file2' file3
    running 2 files
    running file1 file2 file3

----------------------------------------------------------------------------

-> Introducing $@ <-
====================

When processing a list of arguments with a built-in construct such as
the `for` loop, it is often valuable to use the `$@` variable instead
of `$*`.

While `$*` evaluates to `'file1 file2 file3'`, `$@` evaluates to
`"file1" "file2" "file3"`, so long as it is surrounded by double quotes.

----------------------------------------------------------------------------

-> Introducing $@ <-
====================

    session6$ cat run3
    echo "running $# files"
    for file in "$@"
    do
        echo "running $file"
        #do something
    done

----------------------------------------------------------------------------

-> Introducing $@ <-
====================

    session6$ ./run3 file1 file2 file3
    running 3 files
    running file1
    running file2
    running file3
    session6$ ./run3 'file1 file2' file3
    running 2 files
    running file1 file2
    running file3

----------------------------------------------------------------------------

-> Introducing $@ <-
====================

It should be noted, however, that `$@` without double-quotes performs
exactly as `$*`.

    session6$ cat run4
    echo "running $# files"
    for file in $@
    do
        echo "running $file"
        #do something
    done

----------------------------------------------------------------------------

-> Introducing $@ <-
====================

    session6$ ./run4 file1 file2 file3
    running 3 files
    running file1
    running file2
    running file3
    session6$ ./run4 'file1 file2' file3
    running 2 files
    running file1
    running file2
    running file3

----------------------------------------------------------------------------

-> Implicit $@ <-
=================

Processing arguments with `"$@"` is so common that modern versions of
`bash` provide a construct for using `for` loops without an argument
list that processes them as if they were provided an `in "$@"` statement.

    session6$ cat run5
    echo "running $# files"
    for file
    do
        echo "running $file"
        #do something
    done

----------------------------------------------------------------------------

-> Implicit $@ <-
=================

    beast-at-tanagra:session6 [master !?>] $ ./run5 file1 file2 file3
    running 3 files
    running file1
    running file2
    running file3
    beast-at-tanagra:session6 [master !?>] $ ./run5 'file1 file2' file3
    running 2 files
    running file1 file2
    running file3

----------------------------------------------------------------------------

-> The while Construct <-
=========================

The `while` construct provides iteration while its test condition
remains true. The block of commands will execute infinitely so long as
the test condition provided before the block remains true.

while *command*
do
  *command*
  *command*
  *command*
done

----------------------------------------------------------------------------

-> The while Construct <-
=========================

    session6$ i=1
    session6$ while [ "$i" -le 5 ]
    > do
    >   echo $i
    >   i=$((i + 1))
    > done
    1
    2
    3
    4
    5

----------------------------------------------------------------------------

-> The until Construct <-
=========================

The `until` construct works similarly to `while`, but the test
condition is reversed. The block of commands will execute infinitely
so long as the test condition provided before the block remains false.

    session6$ echo $i
    6
    session6$ until [ "$i" -le 1 ]
    > do
    >   echo $i
    >   i=$((i - 1))
    > done
    6
    5
    4
    3
    2

----------------------------------------------------------------------------

-> An actual script <-
======================

    session6$ cat waitfor
    #!/usr/bin/env bash
    if [ $# -ne 1 ]
    then
        echo "Usage: waitfor username"
        exit 1
    fi

    user="$1"

    until who | grep "^$user " > /dev/null
    do
        sleep 60
    done

    echo "$user has come online!"

----------------------------------------------------------------------------

-> break Statements <-
======================

The `break` statement is used to exit a loop early. Executing `break`
ends the current block of iteration immediately.

    session6$ for i in 1 2 3 4 5
    > do
    >   if [ $i -ge 3 ]
    >   then
    >     break
    >   else
    >     echo $i
    >   fi
    > done
    1
    2

----------------------------------------------------------------------------

-> break Statements <-
======================

The `break` statement is quite handy at breaking out of what would
otherwise be an infinite loop.

    session6$ while :
    > do
    >   if [ "$i" -le 0 ]
    >   then
    >     break
    >   else
    >     echo $i
    >     i=$((i - 1))
    >   fi
    > done
    3
    2
    1

----------------------------------------------------------------------------

-> continue Statements <-
=========================

The `continue` statement is used to skip part of a loop's block of
commands. Unlike `break`, `continue` does not exit the loop. Rather,
`continue` skips the remaining commands in the block and goes back to
the test condition or next value.

    session6$ for i in 1 2 3 4 5 6
    > do
    >   if [ $((i % 2)) -eq 0 ]
    >   then
    >     echo "$i is even"
    >   else
    >     continue
    >   fi
    > done
    2 is even
    4 is even
    6 is even

----------------------------------------------------------------------------

-> Loop Background Processing <-
================================

Modifiers that are normally placed after a command such as `&` for
sending a process to the background or I/O redirection can be placed
after the `done` statement of a loop and effect the whole loop statement.

    for i in 1 2 3 4 5
    do
      echo $i
    done &

----------------------------------------------------------------------------

-> Loop I/O Redirection <-
==========================

Much like `&` for backgrounding a process, the output of a loop may be
redirected using `>`.

    session6$ for i in 1 2 3 4 5
    > do
    >   echo $i
    > done > somenumbers
    session6$ cat somenumbers
    1
    2
    3
    4
    5

----------------------------------------------------------------------------

-> Loop I/O Piping <-
=====================

The same redirection after a loop rule applies to the pipe, as the
internal mechanism for a pipe `|` and redirection to a file `>` is no
different.

    session6$ for i in 1 2 3 4 5; do echo $i
    > done | wc -l
           5

----------------------------------------------------------------------------

-> getopts <-
=============

With the new-found knowledge we have about processing arguments, it
might be tempting to write a script that processes options as such:

    session6$ cat dateopts1
    for opt in "$@"
    do
        if [ $opt = "-d" ]
        then
            echo -n "$(date +'%e') "
        elif [ $opt = "-m" ]
        then
            echo -n "$(date +'%b') "
        elif [ $opt = "-y" ]
        then
            echo -n "$(date +'%Y') "
        else
            echo -n $(date +"$*")
        fi
    done
    echo

----------------------------------------------------------------------------

-> getopts <-
=============

This works kind of alright, but falls short once we want to support
standard Unix option formats.

    session6$ ./dateopts1 -d
    8
    session6$ ./dateopts1 -m
    Sep
    session6$ ./dateopts1 -d -m
    8 Sep
    session6$ ./dateopts1 "%m-%d-%y"
    09-08-17
    session6$ ./dateopts1 -dmy
    -dmy

----------------------------------------------------------------------------

-> getopts <-
=============

Following the same pattern gets cumbersome if we want to support
options that have their own arguments such as moving the default case
to a `-f` option and instead printing a usage message if there are no options.

    session6$ cat dateopts2
    for opt in "$@"
    do
        # ...
        elif [ $opt = "-f" ]
        then
            FMTNEXT=true
        elif [ $FMTNEXT = true ]
        then
            unset FMTNEXT
            echo -n "$(date +$opt)"
        else
            echo "Usage: dateopts2 [-m] [-d] [-y] [-f format]"
            echo "-d print the day"
            echo "-m print the month"
            echo "-y print the year"
            echo "-f fmt prints the results of providing the fmt string to date"
        fi
    done
    echo

----------------------------------------------------------------------------

-> getopts <-
=============

This is where the `getopts` built-in comes into play. `getopts`
provides the option parsing functionality we have come to expect from
standard Unix tools.

`getopts` takes a list of options that are available to the script, as
well as an identifier to which each option will be bound as it is
parsed. Options which take their own parameters are followed by a
`:`. Each time `getopts` is called, it will return a `0` exit status
unless there are no options left to parse. This means that using
`getopts` in a while loop is highly effective.

----------------------------------------------------------------------------

-> getopts <-
=============

    session6$ cat dateopts3
    while getopts mdyf: option
    do
        case "$option"
        in
            d) echo -n "$(date +%e) ";;
            m) echo -n "$(date +%b) ";;
            y) echo -n "$(date +%Y) ";;
            f) echo -n $(date +"$OPTARG");;
            \?) echo "Usage: dateopts2 [-m] [-d] [-y] [-f format]";
                echo "-d print the day";
                echo "-m print the month";
                echo "-y print the year";
                echo "-f fmt prints the results of providing the fmt \
                         string to date";;
        esac
    done
    echo
