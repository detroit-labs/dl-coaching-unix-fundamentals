%title: Unix Fundamentals Session 5
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 5 <-
---------------

-> A Detroit Labs Developer Coaching Course <-

----------------------------------------------------------------------------

-> Dealing with arguments <-
============================

When dealing with arguments to a shell script, the shell assigns
special variables to capture and handle arguments.

    session5$ cat args
    echo $# arguments passed
    echo arg 1 = :$1: arg 2 = :$2: arg 3 = :$3:
    session5$ ./args a b c
    3 arguments passed
    arg 1 = :a: arg 2 = :b: arg 3 = :c:
    session5$ ./args a b
    2 arguments passed
    arg 1 = :a: arg 2 = :b: arg 3 = ::
    session5$ ./args
    0 arguments passed
    arg 1 = :: arg 2 = :: arg 3 = ::

`$#` stores the number of arguments provided to a shell script.
`$1`, `$2` and so on with the ordinal variables store each argument.

----------------------------------------------------------------------------

-> Dealing with arguments <-
============================

The `$*` special variable captures the entire argument list for a
script as a string.

    session5$ cat args2
    echo $# arguments passed
    echo they are :$*:
    session5$ ./args2
    0 arguments passed
    they are ::
    session5$ ./args2 a b c d e
    5 arguments passed
    they are :a b c d e:

----------------------------------------------------------------------------

-> Exercise <-
==============

Can we write a script that adds entries to the phonebook?

----------------------------------------------------------------------------

-> Exercise <-
==============

Can we write a script that removes entries from the phonebook?

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

    session5$ echo "derp"
    derp
    session5$ echo $?
    0
    session5$ grep derp phonebook
    session5$ echo $?
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

    session5$ cat online
    user=$1
    if who | grep "$user"
    then
    echo "$user is online"
    fi
    session5$ ./online sleepynate
    sleepynate   console  May 24 07:34
    sleepynate   ttys000  May 24 07:40
    sleepynate is online
    session5$ ./online barney

Unfortunately, the default output of the commands makes our script
look a little unprofessional here.

----------------------------------------------------------------------------

-> The if Construct <-
======================

    session5$ cat online2
    user=$1
    if who | grep "^$user " > /dev/null
    then
    echo "$user is online"
    fi
    session5$ ./online2 barney
    session5$ ./online2 sleepynate
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

    session5$ test "bob" = "bob"
    session5$ echo $?
    0
    session5$ test "bob" = "tom"
    session5$ echo $?
    1

----------------------------------------------------------------------------

-> The test Command <-
======================

You'll notice that paying special attention to quoting will be a
common theme throughout the process of learning shell programming.

    session5$ name="bob"
    session5$ test $name = "bob"
    session5$ echo $?
    0
    session5$ name=
    session5$ test $name = "bob"
    bash: test: =: unary operator expected
    session5$ test "$name" = "bob"
    session5$ echo $?
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

    session5$ cat isitbob
    if test $1 = "bob"
    then
        echo "hey it is bob"
    else
        echo "nope not bob"
    fi
    session5$ ./isitbob "bob"
    hey it is bob
    session5$ ./isitbob "notbob"
    nope not bob

----------------------------------------------------------------------------

-> The test Command <-
======================

There is an alternate form of the `test` command that is much more
common in scripts, and it takes the form of `[ condition ]`

    session5$ cat isitbob2
    if [ $1 = "bob" ]
    then
        echo "hey it is bob"
    else
        echo "nope not bob"
    fi
    session5$ ./isitbob2 bob
    hey it is bob
    session5$ ./isitbob2 jane
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

`[ \! -e "../session5/session5.md" ]` is true if the file does *not* exist.
`[ $i -gt 0 -a $i -le 10 ]` is true if `$i` is between 1 and 10.
`[ -d $file -o -L $file ]` is true if `$file` is a directory *or* a symlink.

----------------------------------------------------------------------------

-> The exit Command <-
======================

The `exit` command emits an exit status from the current shell and
terminates the process.

To tell our parent process that we're exiting normally

    session5$ exit 0

To tell our parent process that something terrible has happened

    session5$ exit 1
    session5$ exit 127
    session5$ exit -1

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

    session5$ cat isitbob3
    if [ $1 = "bob" ]
    then
        echo "hey it is bob"
    elif [ $1 = "BOB" ]
    then
        echo "OH HI BOB"
    else
        echo "nope not bob"
    fi
    session5$ ./isitbob3 bob
    hey it is bob
    session5$ ./isitbob3 BOB
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

    session5$ cat caseme
    case $1 in
        1) echo "you are number 1"; echo "good job.";;
        2) echo "i like you two";;
        [0-9]) echo "those look like numbers";;
        "outside") echo "how about match?";;
        *) echo "wat.";;
    esac
    session5$ ./caseme 1
    you are number 1
    good job.
    session5$ ./caseme 2
    i like you two
    session5$ ./caseme 3
    those look like numbers
    session5$ ./caseme outside
    how about match?
    session5$ ./caseme dhsajkdjsakljdklsajdklsa
    wat.

----------------------------------------------------------------------------

-> The && and || Constructs <-
==============================

There are two built-in shell constructs that allow for conditional
execution of commands base on the success or failure of their arguments.

The `&&` construct will only execute its right-hand argument if its
left-hand argument is true.

    session5$ who | grep "nate" > /dev/null && echo "nate is online"
    nate is online

The `||` construct will only execute its right-hand argument if its
left-hand argument is false.

    session5$ who | grep "janet" > /dev/null || echo "janet is online"
    janet is online
