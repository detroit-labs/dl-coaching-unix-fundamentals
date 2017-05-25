%title: Unix Fundamentals Session 3
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 3 <-
------------------

-> A Detroit Labs Developer Coaching Course <-

----------------------------------------------------------------------------

-> Quoting <-
=============

The shell uses *quoting* to help extend and/or give clarification to
commands and their arguments. There are 4 standard quoting mechanisms
used in shell programming.

- `'` single quote
- `"` double quote
- `\\` backslash
- `\`` back quote

----------------------------------------------------------------------------

-> Argument Grouping <-
=======================

Quoting is often used to control grouping of arguments, especially
arguments that contain whitespace.

    session3$ grep Sonar phonebook
    Brenda Sonars	201-555-5972
    session3$ grep Lefty phonebook
    Lefty Sanchez	221-555-9282

    session3$ grep Steve Govad phonebook
    grep: Govad: No such file or directory
    phonebook:Steve Govad	210-555-7677

    session3$ grep 'Steve Govad' phonebook
    Steve Govad	210-555-7677

----------------------------------------------------------------------------

-> Single Quotes <-
===================

Single quotes prevent evaluation of any special characters by the shell.

    session3$ file=/users/steve/bin/prog1
    session3$ echo $file
    /users/steve/bin/prog1
    session3$ echo '$file'
    $file
    session3$ echo *
    args args2 online online2 phonebook session3.md
    session3$ echo '*'
    *
    session3$ echo '< > | ; ( ) { } > > " ` &'
    < > | ; ( ) { } > > " ` &

----------------------------------------------------------------------------

-> Double Quotes <-
===================

Double quotes work like single quotes, but do evaluate some special
characters, specifically `$`, `\\` and `\``.

    session3$ message='She is swell on C shells by the sea, sure.'
    session3$ echo $message
    She is swell on C shells by the sea, sure.
    session3$ text='* is all your files, basically'
    session3$ echo $text
    args args2 online online2 phonebook session3.md is all your
    files, basically
    session3$ x=*
    session3$ echo $x
    args args2 online online2 phonebook session3.md
    session3$ echo '$x'
    $x
    session3$ echo "$x"
    *

----------------------------------------------------------------------------

-> Double Quotes <-
===================

    session3$ poem="some strings are read
    > others, line too."
    session3$ echo $poem
    some strings are read others, line too.
    session3$ echo "$poem"
    some strings are read
    others, line too.

    session3$ echo $text
    args args2 online online2 phonebook session3.md is all your
    files, basically
    session3$ echo "$text"
    \* is all your files, basically

----------------------------------------------------------------------------

-> Mixing Quotes <-
===================

Single and double quotes can be used to negate any special meaning
from the other.

    session3$ echo '"Dotz!" said Hughes, "What are you doing?"'
    "Dotz!" said Hughes, "What are you doing?"
    session3$ echo "to which I replied 'Just teaching Unix, sir.'"
    to which I replied 'Just teaching Unix, sir.'

----------------------------------------------------------------------------

-> The Backslash <-
===================

As with RegEx, the backslash `\\` is used to escape single characters
that would otherwise be interpreted as special characters on the
command line.

    session3$ echo >
    bash: syntax error near unexpected token `newline'
    session3$ echo \>
    >

    session3$ x=*
    session3$ echo $x
    args args2 online online2 phonebook session3.md
    session3$ echo \$x
    $x

    session3$ echo \\
    \
    session3$ echo '\'
    \

----------------------------------------------------------------------------

-> The Backslash <-
===================

We've already seen that the backslash can be used to break lines and
create single commands from multi-line input. Now it is clear that
this is because it escapes the newline character at the end of a line.

    session3$ echo A line ends \
    > on when a backslash \
    > is absent.
    A line ends on when a backslash is absent.

    session3$ grep Albert\ \
    > Chiba phonebook
    Albert Chiba	973-555-2051

----------------------------------------------------------------------------

-> The Backslash <-
===================

Inside double quotes, the backslash is used to escape characters that
would normally be evaluated as special characters.

    session3$ echo "\$x"
    $x
    session3$ echo "\ is the backslash character"
    \ is the backslash character
    session3$ echo "The value of x is \"$x\""
    The value of x is "*"

The same does not hold true for single quotes. If the output requires
single-quotes, it's best to escape them independently.

    session3$ echo '\'I can\'t believe it!\', she said.'
    bash: !\',: event not found
    session3$ echo \'I can\'t believe it\!\', she said.
    'I can't believe it!', she said.

----------------------------------------------------------------------------

-> The Back-Quote <-
====================

Back-quotes are the original method of doing command substitution in
the Unix shell. The result of any command surrounded by back-quotes
will be evaluated and returned as a string.

    session3$ echo There are currently `who | wc -l` people logged in.
    There are currently 5 people logged in.

    session3$ echo "The current time is: `date +%H:%M`"
    The current time is: 09:13

----------------------------------------------------------------------------

-> Command Substitution <-
==========================

Due to internationalization, the back-quote has fallen out of favor
for command substitution. Now, the command-substitution `$(...)`
construct is preferred.

    session3$ echo There are currently $(who | wc -l) people logged in.
    There are currently 5 people logged in.

    session3$ echo "The current time is: $(date +%H:%M)"
    The current time is: 09:18

Single-quotes can still be used to prevent evaluation.

    session3$ echo '$(who | wc -l)' will show logged in users.
    $(who | wc -l) will show logged in users.

----------------------------------------------------------------------------

-> Command Substitution <-
==========================

A common usage for command substitution in shell scripting is to
capture the results of executing a command in a variable.

    session3$ myfiles=$(ls)
    session3$ echo $myfiles
    args args2 online online2 phonebook session3.md

----------------------------------------------------------------------------

-> expr <-
==========

Nowadays most shells support arithmetic evaluation with a native
construct. However old shells do not support this. To support old
systems, it's important to learn the `expr` command.

    session3$ expr 1 + 2
    3
    session3$ expr 50 / 2 + 5
    30

Quoting will be important in evaluating `expr` though.

    session3$ expr 20 * 5
    expr: syntax error
    session3$ expr "20 * 5"
    20 * 5
    session3$ expr 20 \* 5
    100

----------------------------------------------------------------------------

-> expr <-
==========

You'll see this construct used regularly in scripts that carry out a
number of operations and accumulate results as they go. For supporting
older systems, you're much more likely to see the older back-quote
construct.

    session3$ i=1
    session3$ i=$(expr $i + 1)
    session3$ echo $i
    2
    session3$ i=`expr $i + 1`
    session3$ echo $i
    3

----------------------------------------------------------------------------

-> Dealing with arguments <-
============================

When dealing with arguments to a shell script, the shell assigns
special variables to capture and handle arguments.

    session3$ cat args
    echo $# arguments passed
    echo arg 1 = :$1: arg 2 = :$2: arg 3 = :$3:
    session3$ ./args a b c
    3 arguments passed
    arg 1 = :a: arg 2 = :b: arg 3 = :c:
    session3$ ./args a b
    2 arguments passed
    arg 1 = :a: arg 2 = :b: arg 3 = ::
    session3$ ./args
    0 arguments passed
    arg 1 = :: arg 2 = :: arg 3 = ::

`$#` stores the number of arguments provided to a shell script.
`$1`, `$2` and so on with the ordinal variables store each argument.

----------------------------------------------------------------------------

-> Dealing with arguments <-
============================

The `$*` special variable captures the entire argument list for a
script as a string.

    session3$ cat args2
    echo $# arguments passed
    echo they are :$*:
    session3$ ./args2
    0 arguments passed
    they are ::
    session3$ ./args2 a b c d e
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
