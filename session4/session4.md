%title: Unix Fundamentals Session 4
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 4 <-
---------------

-> A Detroit Labs Developer Coaching Course <-

----------------------------------------------------------------------------

-> Writing Programs <-
======================

    session4$ cat > nu
    who | wc -l
    # ^D
    session4$ ./nu
    bash: ./nu: Permission denied

Because we just created `nu` as a regular text file, we don't have
permission to run it as a program.

    session4$ ls -l nu
    -rw-r--r--  1 sleepynate  staff  12 May 16 21:44 nu
    session4$ chmod +x nu
    session4$ ls -l nu
    -rwxr-xr-x  1 sleepynate  staff  12 May 16 21:44 nu
    session4$ ./nu
        4

----------------------------------------------------------------------------

-> Writing Programs <-
======================

We create variables within the current shell environment by providing
an *identifier* followed immediately by an equal sign `=`, followed by
the value that we want to store in that variable.

    session4$ count=1
    session4$ my_bin=~/.bin
    session4$ echo $my_bin
    /Users/sleepynate/.bin

    session4$ cd $my_bin
    .bin$ pwd
    /Users/sleepynate/.bin
    .bin$

    session4$ echo $count $count $count $count
    1 1 1 1

----------------------------------------------------------------------------

-> Writing Programs <-
======================

    session4$ command=wc
    session4$ option=-l
    session4$ file=intro
    session4$ $command $option $file
        6 intro

We may come across a situation where we need to use a variable where
it could be confused with another identifier. For this, we have the
`${}` construct.

    session4$ echo $file_backup

    session4$ echo ${file}_backup
    intro_backup
    session4$ cp $file ${file}_backup

----------------------------------------------------------------------------

-> Arithmetic Operations <-
===========================

We can perform arithmetic with values on the command line using the
`$(())` construct.

    session4$ echo $((69 + 31))
    100
    session4$ echo $((7 * 6))
    42
    session4$ echo $count
    1
    session4$ count=$(($count + 1))
    session4$ echo $count
    2
    session4$ count=$(($count + 1))
    session4$ echo $count
    3

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

    session4$ grep Sonar phonebook
    Brenda Sonars	201-555-5972
    session4$ grep Lefty phonebook
    Lefty Sanchez	221-555-9282

    session4$ grep Steve Govad phonebook
    grep: Govad: No such file or directory
    phonebook:Steve Govad	210-555-7677

    session4$ grep 'Steve Govad' phonebook
    Steve Govad	210-555-7677

----------------------------------------------------------------------------

-> Single Quotes <-
===================

Single quotes prevent evaluation of any special characters by the shell.

    session4$ file=/users/steve/bin/prog1
    session4$ echo $file
    /users/steve/bin/prog1
    session4$ echo '$file'
    $file
    session4$ echo *
    phonebook session4.md
    session4$ echo '*'
    *
    session4$ echo '< > | ; ( ) { } > > " ` &'
    < > | ; ( ) { } > > " ` &

----------------------------------------------------------------------------

-> Double Quotes <-
===================

Double quotes work like single quotes, but do evaluate some special
characters, specifically `$`, `\\` and `\``.

    session4$ message='She is swell on C shells by the sea, sure.'
    session4$ echo $message
    She is swell on C shells by the sea, sure.
    session4$ text='* is all your files, basically'
    session4$ echo $text
    phonebook session4.md is all your files, basically
    session4$ x=*
    session4$ echo $x
    phonebook session4.md
    session4$ echo '$x'
    $x
    session4$ echo "$x"
    *

----------------------------------------------------------------------------

-> Double Quotes <-
===================

    session4$ poem="some strings are read
    > others, line too."
    session4$ echo $poem
    some strings are read others, line too.
    session4$ echo "$poem"
    some strings are read
    others, line too.

    session4$ echo $text
    phonebook session4.md is all your files, basically
    session4$ echo "$text"
    \* is all your files, basically

----------------------------------------------------------------------------

-> Mixing Quotes <-
===================

Single and double quotes can be used to negate any special meaning
from the other.

    session4$ echo '"Dotz!" said Hughes, "What are you doing?"'
    "Dotz!" said Hughes, "What are you doing?"
    session4$ echo "to which I replied 'Just teaching Unix, sir.'"
    to which I replied 'Just teaching Unix, sir.'

----------------------------------------------------------------------------

-> The Backslash <-
===================

As with RegEx, the backslash `\\` is used to escape single characters
that would otherwise be interpreted as special characters on the
command line.

    session4$ echo >
    bash: syntax error near unexpected token `newline'
    session4$ echo \>
    >

    session4$ x=*
    session4$ echo $x
    phonebook session4.md
    session4$ echo \$x
    $x

    session4$ echo \\
    \
    session4$ echo '\'
    \

----------------------------------------------------------------------------

-> The Backslash <-
===================

We've already seen that the backslash can be used to break lines and
create single commands from multi-line input. Now it is clear that
this is because it escapes the newline character at the end of a line.

    session4$ echo A line ends \
    > on when a backslash \
    > is absent.
    A line ends on when a backslash is absent.

    session4$ grep Albert\ \
    > Chiba phonebook
    Albert Chiba	973-555-2051

----------------------------------------------------------------------------

-> The Backslash <-
===================

Inside double quotes, the backslash is used to escape characters that
would normally be evaluated as special characters.

    session4$ echo "\$x"
    $x
    session4$ echo "\ is the backslash character"
    \ is the backslash character
    session4$ echo "The value of x is \"$x\""
    The value of x is "*"

The same does not hold true for single quotes. If the output requires
single-quotes, it's best to escape them independently.

    session4$ echo '\'I can\'t believe it!\', she said.'
    bash: !\',: event not found
    session4$ echo \'I can\'t believe it\!\', she said.
    'I can't believe it!', she said.

----------------------------------------------------------------------------

-> The Back-Quote <-
====================

Back-quotes are the original method of doing command substitution in
the Unix shell. The result of any command surrounded by back-quotes
will be evaluated and returned as a string.

    session4$ echo There are currently `who | wc -l` people logged in.
    There are currently 5 people logged in.

    session4$ echo "The current time is: `date +%H:%M`"
    The current time is: 09:13

----------------------------------------------------------------------------

-> Command Substitution <-
==========================

Due to internationalization, the back-quote has fallen out of favor
for command substitution. Now, the command-substitution `$(...)`
construct is preferred.

    session4$ echo There are currently $(who | wc -l) people logged in.
    There are currently 5 people logged in.

    session4$ echo "The current time is: $(date +%H:%M)"
    The current time is: 09:18

Single-quotes can still be used to prevent evaluation.

    session4$ echo '$(who | wc -l)' will show logged in users.
    $(who | wc -l) will show logged in users.

----------------------------------------------------------------------------

-> Command Substitution <-
==========================

A common usage for command substitution in shell scripting is to
capture the results of executing a command in a variable.

    session4$ myfiles=$(ls)
    session4$ echo $myfiles
    phonebook session4.md

----------------------------------------------------------------------------

-> expr <-
==========

Nowadays most shells support arithmetic evaluation with a native
construct. However old shells do not support this. To support old
systems, it's important to learn the `expr` command.

    session4$ expr 1 + 2
    3
    session4$ expr 50 / 2 + 5
    30

Quoting will be important in evaluating `expr` though.

    session4$ expr 20 * 5
    expr: syntax error
    session4$ expr "20 * 5"
    20 * 5
    session4$ expr 20 \* 5
    100

----------------------------------------------------------------------------

-> expr <-
==========

You'll see this construct used regularly in scripts that carry out a
number of operations and accumulate results as they go. For supporting
older systems, you're much more likely to see the older back-quote
construct.

    session4$ i=1
    session4$ i=$(expr $i + 1)
    session4$ echo $i
    2
    session4$ i=`expr $i + 1`
    session4$ echo $i
    3
