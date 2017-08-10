%title: Unix Fundamentals Session 3
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 3 <-
------------------

-> A Detroit Labs Developer Coaching Course <-

----------------------------------------------------------------------------

-> Regular Expressions <-
=========================

Regular Expressions or *regex* are a family of languages for searching
and altering plain text. Regex are common in text editing tools,
search engines, and embedded in nearly every programming
language. They are used by a wide variety of Unix tools to deal with
patterns of text, so mastering regular expressions will be vital to
working effectively and efficiently in a Unix environment.

----------------------------------------------------------------------------

-> Regular Expression Basics <-
===============================

The forward slash is the standard delimiter for regex. The search
pattern is placed between said delimiters.

`/Hughes/` would match the string "Hughes".

All regular expression patterns are case-sensitive.

----------------------------------------------------------------------------

-> Regular Expression Basics <-
===============================

The carat `^` matches the beginning of a line.

`/^The/` matches the word "The", if it is at the beginning of a line.

The dollar sign `$` matches the end of a line.

`/End$/` matches the word "End", if it is at the end of a line.

Therefore `/^The End$/` matches the entire line "The End", if it is
the only thing on the line.

Likewise, `/^$/` matches empty lines.

----------------------------------------------------------------------------

-> Regular Expression Wildcards <-
==================================

The dot `.` matches exactly one of any character.

`/r./` would match `r` followed by any other character.
`/ ... /` would match 3 characters with a single space before and after.

The asterisk `*` matches zero or more of the preceding character.

`/d.*z/` would match a d, followed by any number of characters,
followed by a z. It would match against "dotz", "dragonball z" or even
just "dz".

`/=*/` would match any line, because every line contains at least 0
'=' in it. You might have meant `/==*/` for "at least one =", but
we'll see a better way of doing this soon.

----------------------------------------------------------------------------

-> Regular Expression Sets and Ranges <-
========================================

Square brackets `[...]` are used to provide a set or range of characters
to match. This is called a character class.

`/[sStT]he/` would match "she", "She", "the" or "The".

Note that a character class will only match a single character. Thus,
`/gr[ea]y/` will match "grey" or "gray", but not "graaay" or "greay".

For convenience, a character class can contain ranges of
characters. To match every uppercase letter, we could use `/[A-Z]/` or
to match every digit, `/[0-9]/`.

----------------------------------------------------------------------------

-> Regular Expression Sets and Ranges <-
========================================

We can also negate the class, by starting it with a carat, such as the
regex `/[^a-z]/`, which would match a single character that is NOT a
lowercase letter.

In the context of a character class, several special characters have
no special meaning, for example, `/[*]/` would match just the asterisk
character. Outside of a character class, we'd need to "escape" the
asterisk to take away its special meaning, such as `/\\*/`.

If you need to match against the right bracket or hyphen, they need to
be the character in the group, as in `/[-0-9]/`. If you need to negate
the class, the carat comes first, as in `/[^]a-z]/`.

----------------------------------------------------------------------------

-> Matching specific numbers of characters <-
=============================================

Rather than a single character or zero-to-many characters, we can
match against specific numbers of characters using curly braces `{}`.
Due to parsing rules, in the vast majority of implementations, these
characters will need to be escaped.

`/Z\\{1,5\\}/` matches between one and 5 Zs

`/[-0-9]\\{10\\}/` matches exactly 10 digits or hyphens

`/[0-9]\\{3\\}-[0-9]\\{3\\}-[0-9]\\{4\\}/` matches USA phone numbers quite well.

----------------------------------------------------------------------------

-> Match groups <-
==================

The parenthesis `()` can be used to create match groups. Matched
patterns are stored in *registers* numbered 1 to 9. These registers
can be used to recall the matched pattern later.

`/^\\(.\\)/` captures the first character of a line.

`/^\\(.\\)\\1/` matches the first 2 characters of a line, if they are the
same.

`/^\\(.\\).*\\1$/` matches a line in which the first and last characters
are the same.

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

`cut` is used to cut specific pieces of text from an input.

    session3$ cat phonebook
    Albert Chiba	973-555-2051
    Brenda Sonars	201-555-5972
    Lefty Sanchez	221-555-9282
    Steve Govad	210-555-7677
    Tonya Isotalo	739-555-9152

    session3$ cut -c 1,5,10,15 phonebook
    Ari7
    Bdn2
    Lyc2
    Sea0
    Tat7

-c = characters
-d = delimiter
-f = field

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

    session3$ cut -c 13- phonebook
        973-555-2051
    s	201-555-5972
    z	221-555-9282
    210-555-7677
    o	739-555-9152

    session3$ cut -d: -f1,6,7 /etc/passwd
    nobody:/var/empty:/usr/bin/false
    root:/var/root:/bin/sh
    daemon:/var/root:/usr/bin/false
    _uucp:/var/spool/uucp:/usr/sbin/uucico
    _taskgated:/var/empty:/usr/bin/false
    _networkd:/var/networkd:/usr/bin/false

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

`paste` is the opposite of cut. It takes lines from each of its files
arguments and joins them into single lines.

    session3$ cut -f1 phonebook > names
    session3$ paste names addresses
    Albert Chiba    742 Evergreen Terrace, Springfield
    Brenda Sonars   93 College Place, New Amsterdam
    Lefty Sanchez   71 W. 52nd Street, New Amsterdam
    Steve Govad     83 Chancery St., Hensonburst
    Tonya Isotalo   71 W. 52nd Street, New Amsterdam

-d = delimiter
-s = squash to a single line

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

The `-` option can be used with many text tools that take multiple
files as parameters. It causes the tool to read from standard input
for whichever argument it takes the place of.

    session3$ cut -f1 phonebook | paste - addresses
    Albert Chiba    742 Evergreen Terrace, Springfield
    Brenda Sonars   93 College Place, New Amsterdam
    Lefty Sanchez   71 W. 52nd Street, New Amsterdam
    Steve Govad     83 Chancery St., Hensonburst
    Tonya Isotalo   71 W. 52nd Street, New Amsterdam

    session3$ cut -f1 phonebook | paste -s -
    Albert Chiba Brenda Sonars Lefty Sanchez Steve Govad Tonya Isotalo

    session3$ ls | paste -d+ -s -
    addresses+intro+phonebook+session3.md

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

`sed` is the Unix *stream editor*. Most commonly, it's used to alter
text sent to `stdin` and output to `stdout`.

    session3$ cat intro
    Detroit Labs was founded by Paul Glomski, Dan
    Ward, Nathan Hughes and Henry Balanon in Detroit,
    MI in 2011. One of the primary goals in the
    creation of Detroit Labs was to write really
    cool software in a really cool environment,
    and also to make money.

    session3$ sed 's/Labs/LABS/' intro
    Detroit LABS was founded by Paul Glomski, Dan
    Ward, Nathan Hughes and Henry Balanon in Detroit,
    MI in 2011. One of the primary goals in the
    creation of Detroit LABS was to write really
    cool software in a really cool environment,
    and also to make money.

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

sed's `s` command takes a regex and substitution. The first match of the
regex on each line is replaced with the substitution. Adding a `g`
flag at the end replaces every occurrence.

    session3$ echo "Hello Hello Hello" | sed s/Hello/Hebbo\!/
    Hebbo! Hello Hello
    session3$ echo "Hello Hello Hello" | sed s/Hello/Hebbo\!/g
    Hebbo! Hebbo! Hebbo!
    session3$ echo "Hello Hello Hello" | sed 's/\(He\)/\1\1\1/g'
    HeHeHello HeHeHello HeHeHello

    session3$ who | sed 's/ .*//'
    sleepynate
    sleepynate
    sleepynate
    sleepynate

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

sed's `p` command is used to print selective lines from files. It is
often used in conjunction with the `-n` option, which suppresses
printing of lines that do not match the pattern.

    session3$ sed -n '1,2p' intro
    Detroit Labs was founded by Paul Glomski, Dan
    Ward, Nathan Hughes and Henry Balanon in Detroit,

    session3$ sed -n '/Labs/p' intro
    Detroit Labs was founded by Paul Glomski, Dan
    creation of Detroit Labs was to write really

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

sed's `d` command is essentially the inverse of print, and instead
deletes matched lines from the output.

    session3$ sed '1,2d' intro
    MI in 2011. One of the primary goals in the
    creation of Detroit Labs was to write really
    cool software in a really cool environment,
    and also to make money.

    session3$ sed '/Labs/d' intro
    Ward, Nathan Hughes and Henry Balanon in Detroit,
    MI in 2011. One of the primary goals in the
    cool software in a really cool environment,
    and also to make money.

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

`tr` is the translate command. It takes two arguments which are lists
of characters. Characters from the first list are replaced with
the respective characters from the second list.

    session3$ echo "hello" | tr l 7
    he77o

    session3$ tr aeou @30V < intro
    D3tr0it L@bs w@s f0Vnd3d by P@Vl Gl0mski, D@n
    W@rd, N@th@n HVgh3s @nd H3nry B@l@n0n in D3tr0it,
    MI in 2011. On3 0f th3 prim@ry g0@ls in th3
    cr3@ti0n 0f D3tr0it L@bs w@s t0 writ3 r3@lly
    c00l s0ftw@r3 in @ r3@lly c00l 3nvir0nm3nt,
    @nd @ls0 t0 m@k3 m0n3y.

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

    session3$ date
    Tue May 16 01:40:02 EDT 2017
    session3$ date | tr ' ' '\11'
    Tue	May	16	01:40:21	EDT	2017
    session3$ date | tr ' ' '\12'
    Tue
    May
    16
    01:40:25
    EDT
    2017

    session3$ sed -n '1,2p' intro | tr '[a-z]' '[A-Z]'
    DETROIT LABS WAS FOUNDED BY PAUL GLOMSKI, DAN
    WARD, NATHAN HUGHES AND HENRY BALANON IN DETROIT,

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

The `-s` option for `tr` squeezes multiple occurrences of the
characters to be translated into a single replacement.

    session3$ echo "lots     of       spaces" | tr -s ' ' ' '
    lots of spaces

The `-d` option for `tr` simply removes any characters it receives as
arguments.

    session3$ echo "lots     of       spaces" | tr -d ' '
    lotsofspaces

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

The `grep` command stands for "Global (search) Reg-Ex and Print",
though it originates from an antiquated editor command, g/re/p. It is
used to search text with regex.

    session3$ sed -n /Glom/p intro
    Detroit Labs was founded by Paul Glomski, Dan
    session3$ grep Glom intro
    Detroit Labs was founded by Paul Glomski, Dan
    session3$

    session3$ grep 'e[nt][ri]' intro
    Detroit Labs was founded by Paul Glomski, Dan
    Ward, Nathan Hughes and Henry Balanon in Detroit,
    creation of Detroit Labs was to write really

    session3$ grep 'Det.*Labs' intro
    Detroit Labs was founded by Paul Glomski, Dan
    creation of Detroit Labs was to write really


----------------------------------------------------------------------------

-> Unix text tools <-
=====================

The `-i` option is for ignoring the case of the search term.

    session3$ grep -i 'hUGHES' intro
    Ward, Nathan Hughes and Henry Balanon in Detroit,

The `-v` option inverts the results of the search.

    session3$ grep -v 7 phonebook
    Lefty Sanchez	221-555-9282

The `-l` option simply lists files that contain the match.

    session3$ grep -l 5 *
    addresses
    phonebook

The `-n` shows the line numbers where the match appears..

    session3$ grep -n -v 7 phonebook
    3:Lefty Sanchez	221-555-9282

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

`sort` basically does what it says on the tin. It takes text and sorts it.

    session3$ cat addresses
    742 Evergreen Terrace, Springfield
    93 College Place, New Amsterdam
    71 W. 52nd Street, New Amsterdam
    83 Chancery St., Hensonburst
    71 W. 52nd Street, New Amsterdam

    session3$ sort addresses
    71 W. 52nd Street, New Amsterdam
    71 W. 52nd Street, New Amsterdam
    742 Evergreen Terrace, Springfield
    83 Chancery St., Hensonburst
    93 College Place, New Amsterdam

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

The `-r` option for `sort` reverses the list.

    session3$ sort -r addresses
    93 College Place, New Amsterdam
    83 Chancery St., Hensonburst
    742 Evergreen Terrace, Springfield
    71 W. 52nd Street, New Amsterdam
    71 W. 52nd Street, New Amsterdam

The `-u` option will only return unique lines, eliminating duplicates.

    session3$ sort -u addresses
    71 W. 52nd Street, New Amsterdam
    742 Evergreen Terrace, Springfield
    83 Chancery St., Hensonburst
    93 College Place, New Amsterdam

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

The `-n` option will sort the columns numerically.

    session3$ sort -n addresses
    71 W. 52nd Street, New Amsterdam
    71 W. 52nd Street, New Amsterdam
    83 Chancery St., Hensonburst
    93 College Place, New Amsterdam
    742 Evergreen Terrace, Springfield

The `-o` option will send `sort` output to the specified file. Unlike
shell output redirection, this does not clobber files and can be used
to rewrite a file in sorted order.

    session3$ sort addresses > addresses # THIS DELETES YOUR FILE

    session3$ sort addresses -o addresses

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

`uniq` is used for dealing with duplicate data. By default, it removes
duplicate consecutive lines so it's often used in conjunction with sort.

    session3$ uniq names
    Nathan
    Paul
    Dan
    Henry
    Nathan
    Terry
    Brian

    session3$ sort names | uniq
    Brian
    Dan
    Henry
    Nathan
    Paul
    Terry

----------------------------------------------------------------------------

-> Unix text tools <-
=====================

The `-d` option is used to find duplicates. Using it will only print
lines which are duplicated.

    session3$ sort names | uniq -d
    Nathan

The `-c` will give you a count of duplicate lines.

    session3$ sort names | uniq -c
    1 Brian
    1 Dan
    1 Henry
    2 Nathan
    1 Paul
    1 Terry
