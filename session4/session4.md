%title: Unix Fundamentals Session 4
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 4 <-
---------------

-> A Detroit Labs Developer Coaching Course <-

----------------------------------------------------------------------------

-> Exit status <-
=================

Every process in Unix emits an *Exit Code* back to the process which
spawned it when it ends. This status is a number intended to indicate
the success or failure of a command. By convention, a `0` exit status
indicates success, whereas any other value indicates an error of some
kind. Often, specific exit statuses that are the result of errors are
listed in a command's manual page.

The exit status of the last command executed by a shell is stored in
the `$?` variable.

    session3$ echo "derp"
    derp
    session3$ echo $?
    0
    session3$ grep derp phonebook
    session3$ echo $?
    1

----------------------------------------------------------------------------

-> The if Construct <-
======================

The `if` construct is a mechanism we use to make decisions when
programming the shell. The `if` construct uses the exit status of
commands provided to it to choose branches of logic to execute.
The `if` statement's general structure is as follows:

if *command*
then
  *command1*
  *command2*
fi

----------------------------------------------------------------------------

-> The if Construct <-
======================

    session3$ cat online
    user=$1
    if who | grep "$user"
    then
    echo "$user is online"
    fi
    session3$ ./online sleepynate
    sleepynate   console  May 24 07:34
    sleepynate   ttys000  May 24 07:40
    sleepynate is online
    session3$ ./online barney

Unfortunately, the default output of the commands makes our script
look a little unprofessional here.

----------------------------------------------------------------------------

-> The if Construct <-
======================

    session3$ cat online2
    user=$1
    if who | grep "^$user " > /dev/null
    then
    echo "$user is online"
    fi
    session3$ ./online2 barney
    session3$ ./online2 sleepynate
    sleepynate is online

We can capture the output of commands we don't want the output from
with the special null device.

----------------------------------------------------------------------------

-> The test Command <-
======================

Often, when creating programs, we'll want to test some criteria for
which there is no specific command. This is why the shell built-in
`test` exists.

Like `expr`, `test` takes a variety of expressions. `test` returns an
exit status representing whether the expression provided to it holds
true or not.

    session3$ test "bob" = "bob"
    session3$ echo $?
    0
    session3$ test "bob" = "tom"
    session3$ echo $?
    1

----------------------------------------------------------------------------

-> The test Command <-
======================

You'll notice that paying special attention to quoting will be a
common theme throughout the process of learning shell programming.

    session3$ name="bob"
    session3$ test $name = "bob"
    session3$ echo $?
    0
    session3$ name=
    session3$ test $name = "bob"
    bash: test: =: unary operator expected
    session3$ test "$name" = "bob"
    session3$ echo $?
    1

----------------------------------------------------------------------------

-> The test Command <-
======================

There are many operators for `test`, but some of the most common for
dealing with strings are:

test *string* = *string*  | true if two strings are the same
test *string* \!= *string* | true if two strings are not the same
test *string*           | true if string is not empty
test -n *string*        | true if string is not empty, and also exists
test -z *string*        | true if string is empty, but exists

----------------------------------------------------------------------------

-> The test Command <-
======================

    session3$ cat isitbob
    if test $1 = "bob"
    then
        echo "hey it is bob"
    else
        echo "nope not bob"
    fi
    session3$ ./isitbob "bob"
    hey it is bob
    session3$ ./isitbob "notbob"
    nope not bob

----------------------------------------------------------------------------

-> The test Command <-
======================

There is an alternate form of the `test` command that is much more
common in scripts, and it takes the form of `[ condition ]`

    session3$ cat isitbob2
    if [ $1 = "bob" ]
    then
        echo "hey it is bob"
    else
        echo "nope not bob"
    fi
    session3$ ./isitbob2 bob
    hey it is bob
    session3$ ./isitbob2 jane
    nope not bob


----------------------------------------------------------------------------

-> The test Command <-
======================

Number operations

*a* -eq *b* | numbers *a* and *b* are equal
*a* -ne *b* | numbers *a* and *b* are not equal
*a* -ge *b* | number *a* is greater or equal to *b*
*a* -le *b* | number *a* is less or equal to *b*
*a* -gt *b* | number *a* is greater than *b*
*a* -lt *b* | number *a* is less than *b*

----------------------------------------------------------------------------

-> The test Command <-
======================

File operations

-d *file* | *file* is a directory
-e *file* | *file* exists
-f *file* | *file* is an ordinary file
-L *file* | *file* is a symlink
-r *file* | *file* is readable
-w *file* | *file* is writable
-x *file* | *file* is executable
-s *file* | *file* is non-zero in size

----------------------------------------------------------------------------

-> And, Or, and Not <-
======================

`test` includes options for combining and negating conditions.

`!` is used to negate conditions.
`-a` provides the logical *and* for conditions.
`-o` provides the logical *or* for conditions.

`[ \! -e "../session3/session3.md" ]` is true if the file does *not* exist.
`[ $i -gt 0 -a $i -le 10 ]` is true if `$i` is between 1 and 10.
`[ -d $file -o -L $file ]` is true if `$file` is a directory *or* a symlink.

----------------------------------------------------------------------------

-> The exit Command <-
======================

The `exit` command emits an exit status from the current shell and
terminates the process.

To tell our parent process that we're exiting normally

    session3$ exit 0

To tell our parent process that something terrible has happened

    session3$ exit 1
    session3$ exit 127
    session3$ exit -1

----------------------------------------------------------------------------

-> The elif Construct <-
========================

if *command*
then
  *command1*
  *command2*
elif *command*
then
  *command1*
  *command2*
fi

----------------------------------------------------------------------------

-> The elif Construct <-
========================

    session4$ cat isitbob3
    if [ $1 = "bob" ]
    then
        echo "hey it is bob"
    elif [ $1 = "BOB" ]
    then
        echo "OH HI BOB"
    else
        echo "nope not bob"
    fi
    session4$ ./isitbob3 bob
    hey it is bob
    session4$ ./isitbob3 BOB
    OH HI BOB

----------------------------------------------------------------------------

-> The case Construct <-
========================

The `case` construct provides a mechanism for complex conditional
statements. `case` statements provide pattern matching against any
value for branching execution.

case *value* in
  *pattern1*) *command*;
            *command*;;

  *pattern2*) *command*;;

  *pattern3*) *command*;
            *command*;;

  \*)        *command*;;

esac

----------------------------------------------------------------------------

-> The case Construct <-
========================

    session4$ cat caseme
    case $1 in
    1) echo "you are number 1"; echo "good job.";;
    2) echo "i like you two";;
    [0-9]) echo "those look like numbers";;
    "outside") echo "how about match?";;
    *) echo "wat.";;
    esac
    session4$ ./caseme 1
    you are number 1
    good job.
    session4$ ./caseme 2
    i like you two
    session4$ ./caseme 3
    those look like numbers
    session4$ ./caseme outside
    how about match?
    session4$ ./caseme dhsajkdjsakljdklsajdklsa
    wat.

----------------------------------------------------------------------------

-> The && and || Constructs <-
==============================

There are two built-in shell constructs that allow for conditional
execution of commands base on the success or failure of their arguments.

The `&&` construct will only execute its right-hand argument if its
left-hand argument is true.

    session4$ who | grep "nate" > /dev/null && echo "nate is online"
    nate is online

The `||` construct will only execute its right-hand argument if its
left-hand argument is false.

    session4$ who | grep "janet" > /dev/null || echo "janet is online"
    janet is online

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

    session4$ for i in 2 3 5 8
    > do
    >   echo $i
    > done
    2
    3
    5
    8

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

    session4$ i=1
    session4$ while [ "$i" -le 5 ]
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

    session4$ echo $i
    6
    session4$ until [ "$i" -le 1 ]
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

    session4$ cat waitfor
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

    session4$ for i in 1 2 3 4 5
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

    session4$ while :
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

    session4$ for i in 1 2 3 4 5 6
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


    session4$ for i in 1 2 3 4 5
    > do
    >   echo $i
    > done > somenumbers
    session4$ cat somenumbers
    1
    2
    3
    4
    5

----------------------------------------------------------------------------

-> Loop I/O Piping <-
=====================

    session4$ for i in 1 2 3 4 5; do echo $i
    > done | wc -l
           5
