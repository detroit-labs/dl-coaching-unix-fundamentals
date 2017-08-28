%title: Unix Fundamentals Session 7
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 7 <-
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

    session7$ read a
    10
    session7$ echo $a
    10
    session7$ read x y
    3 6 9
    session7$ echo $x
    3
    session7$ echo $y
    6 9

----------------------------------------------------------------------------

-> The read Command <-
======================

    session7$ cat safecp
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

    session7$ ./safecp ../session4/online myscript
    session7$ ./safecp ../session4/online myscript
    myscript exists. Overwrite? (y/n) y
    session7$ ./safecp ../session4/online myscript
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

    session7$ echo -e "nathan \f paul"
    nathan
        paul
    session7$ echo -e "nathan \c paul"
    nathan session7$ echo -e "nathan \b\b\b\b paul"
    d paul
    session7$ echo -e "nathan \055 paul"
    nathan - paul

----------------------------------------------------------------------------

-> The safercp Script <-
========================

Let's take a look at the `safercp` script included with this lesson to
get an idea of what a well-rounded interactive program might look like.

----------------------------------------------------------------------------

    session7$ for i in a b c d e; do echo "$i" > $i; done; mkdir mydir
    session7$ ls
    a         c         e         safecp     session7.md
    b         d         mydir     safercp
    session7$ ./safercp a f
    session7$ cat f
    a
    session7$ ./safercp b d
    d already exists. Overwrite? (y/n) n
    session7$ cat d
    d
    session7$ ./safercp b d
    d already exists. Overwrite? (y/n) y
    session7$ cat d
    b
    session7$ ./safercp a mydir/
    session7$ ./safercp b c mydir/
    session7$ ls mydir/
    a       b       c
    session7$ ./safercp d e f
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

    session7$ echo -e "1 2\n3 4\n5 6" |
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

    session7$ while read first last number
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

    session7$ printf "The hexadecimal value for %d is %x\ n" 30 30
    The hexadecimal value for 30 is 1e

    session7$ printf "The unsigned value for %d is %u\ n" –1000 –1000
    The unsigned value for -1000 is 4294966296

    session7$ printf "%50s\n" "Indented to 50 characters"
                            Indented to 50 characters

    session7$ printf "%s\t%s\n" "1" "2 3" "4" "5"
    1       2 3
    4       5

    session7$ printf "%.1f\n" 255 3.5
    255.0
    3.5
