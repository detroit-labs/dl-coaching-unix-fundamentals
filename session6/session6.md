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

    session6$ for i in 1 2 3 4 5; do echo $i
    > done | wc -l
           5
