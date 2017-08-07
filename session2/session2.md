%title: Unix Fundamentals Session 2
%author: Nathan Dotz
%date: 2017-05-11








-> Unix Fundamentals <-
=======================

-> Session 2 <-
------------------

-> A Detroit Labs Developer Coaching Course <-

----------------------------------------------------------------------------

-> Loading Programs <-
======================

The *kernel* is the brain of the Unix system. At boot time, the kernel
is loaded from the disk into *memory* where it resides, convincing the
CPU to process thing on behalf of the Unix system and its users.

Aside from a few services and daemons launched and left running at
after the system loads, the rest of the operating system's *programs*
are stored on *disk*, and only get loaded into memory as needed.

The *shell* is one such program, and to get good at shell programming,
you're going to need to know how the system uses it for you.

----------------------------------------------------------------------------

-> Loading Programs <-
======================

- A connection to the system is created physically or via the network
- `init` launches `getty` which reserves a tty device for the
  connection
- `getty` negotiates the start of the authentication process and
  collects the username, passing it to `login` before exiting
- `login` verifies the credentials with `/etc/passwd` and
  `/etc/shadow`
- `login` either rejects the user or launches the user's `shell`
  before exiting

Most ttys these days are *pseudo tty*'s, as classic hardware terminals
that physically connect to Unix machines have become exceedingly
rare. These used to represent actual physical devices on the Unix
system.

(init here is a general name, but may specifically be `rc`, `init`,
`launchd`, `systemd`, `upstart` or others.)

----------------------------------------------------------------------------

-> Command execution process <-
===============================

1. shell displays command prompt
2. user enters command
3. shell parses command to decide what to do
4. shell finds appropriate program on disk and asks kernel to load it
5. kernel copies program into memory, begins execution as a *process*
6. shell (usually) turns over `stdin` and `stdout` to the process
7. the process terminates, relinquishing control back to the shell,
   and goes back to the first step

----------------------------------------------------------------------------

-> Command parsing <-
=====================

      mv     myfile    myfile.bak
      ↑         ↑         ↑
      |         |         |
    command--argument--argument

     echo   when do   we   eat
      ↑       ↑   ↑    ↑    ↑
      |       |   |    |    |
    command---+--arguments--+

Some commands, like `setenv` and `logout` are built-in to the
shell. The shell does not need to have the kernel load these into
memory, and can execute them directly. Otherwise, their execution
model is effectively the same.

----------------------------------------------------------------------------

-> Glob parsing <-
==================

    echo *
      |
      | Glob expands
      |
      ↓
    echo  dir1 dir2 file1 file2
      ↑     ↑   ↑    ↑    ↑
      |     |   |    |    |
      |     |   |    |    |
    command-+--arguments--+

Globs are first expanded into their arguments *before* being passed to
commands. In this case, `echo` is passed 4 arguments, not one.

----------------------------------------------------------------------------

-> Redirection parsing <-
=========================

    echo Do unix class homework > reminder
      ↑   ↑   ↑     ↑    ↑      ↑       ↑
      |    \  |     |    |      |       |
      |     | |     |    |      |       |
    command-+--arguments-+  operator-argument

Here, the shell parses the command line and recognizes the special
redirection character. It then knows to split the command into an
`echo` command with 4 arguments, and then the redirection operator
with a single argument. The shell then opens a file stream and
connects `stdout` out to it before calling `echo` with its arguments.

---------------------------------------------------------------------------

-> Background Processes <-
==========================

Most commands run in the *foreground* and take over the terminal until
they complete. However, commands can be run in the *background*,
allowing the user to continue to work and enter commands

    dir1$ emacsclient -nw file1 &
    [1] 8578
    dir1$ ps
    PID TTY           TIME CMD
    1284 ttys005    0:00.03 /Applications/iTerm.app/Contents/MacOS/iTerm2
    1457 ttys005    0:00.38 -zsh
    5874 ttys005    0:00.28 bash --login
    8578 ttys005    0:00.00 emacsclient -nw file1
    dir1$ fg
    emacsclient -nw file1

---------------------------------------------------------------------------

-> Background Processes <-
==========================

When a command is sent to the background, it is assigned a job number,
which is displayed along with the process ID.

    dir1$ emacsclient -nw file1 &
    [1] 8578

This process is job number *1* and process ID *8578*


---------------------------------------------------------------------------

-> Background Processes <-
==========================

You can find out all the jobs assigned to the current shell with the
`jobs` command, which shows their job number.

    session1$ sleep 30 &
    [1] 99885
    session1$ sleep 20 &
    [2] 525
    session1$ jobs
    [1]-  Running                 sleep 30 &
    [2]+  Running                 sleep 20 &

---------------------------------------------------------------------------

-> Background Processes <-
==========================

To make a process active again or bring it into the *foreground* we
use the `fg` command. By default, `fg` with no arguments makes the
most recent job attach to the shell.

Calling `fg` with a job number will restore attach a specific job.

    session1$ fg %2
    [2]    525 running    sleep 20

---------------------------------------------------------------------------

-> Background Processes <-
==========================

If a process is attached to the shell, pressing *Ctrl* and *z* will
suspend the process. This is annotated by the shell as *^Z*.

    session1$ fg %2
    [2]    525 running    sleep 20
    ^Z
    [2]  + 525 suspended  sleep 20

A suspended process pauses execution and does nothing until told to do
so otherwise.

---------------------------------------------------------------------------

-> Background Processes <-
==========================

When there is a suspended process, you can reattach it to the shell
using `fg` just as if a process were in the background. However, it
can be placed back into the background with the `bg` command.

    session1$ bg
    [2]  - 525 continued  sleep 20

----------------------------------------------------------------------------

-> Exercise <-
==============

Practice backgrounding, resuming and suspending jobs.

----------------------------------------------------------------------------

-> Basic Unix Editors <-
========================

There are two editors that are installed on nearly every Unix system
you're likely to use in your career, `vi` and `nano` (sometimes as `pico`).

----------------------------------------------------------------------------

-> The nano Editor <-
=====================

`nano` is an extremely simple editor that you're likely to feel the
most immediately comfortable with, as it work similarly to editors
like Notepad on Windows or TextEdit on OSX.

The arrow keys are used to navigate around the text.

`^C` is used to show the position of the cursor in the file.
`^W` is used to search for text
`^K` is used to cut lines, and
`^U` is used to paste them.
`^O` is used to save files.
`^X` is used to exit.

----------------------------------------------------------------------------

-> Exercise <-
==============

Let's explore the `nano` editor.

----------------------------------------------------------------------------

-> The vi Editor <-
===================

`vi` is a much more advanced editor dating back to 1976 that gives us
many more features for finding and editing text in our files. `vi` is
a modal editor, meaning there are different modes for navigating text,
editing text and selecting text.

When you start `vi`, you will be in *normal mode*. This mode accepts
all normal editor commands, movement, and is used to change to other
modes. You can return to *normal mode* from other modes with `ESC`

In *insert mode* the text you type is entered into the buffer.

To make selections of text, *visual mode* is used.

Lastly, *command-line* is used for giving commands to read files,
save, start searches, etc.

----------------------------------------------------------------------------

-> The vi Editor <-
===================

From *normal mode*, pressing `:` will enter *command-line mode* where
we can enter a first command ­ `:edit` (or `:e` for short). The `:e`
command is used to load files, and takes a file name as an argument.

    :edit tamarian.txt

Once a file is loaded, `vi` returns you to *normal mode*. While in
*normal mode*, there are many ways to navigate around text.

`h` moves back a character.
`j` moves down one line.
`k` moves up one line.
`l` moves forward one character.

----------------------------------------------------------------------------

-> The vi Editor <-
===================

From *normal mode* pressing `i` will enter *insert mode*. In insert
mode, you can type at the cursor. Once you are happy with your
editing, pressing `ESC` will return you to *normal mode*.

If you are unsatisfied with your changes, pressing `u` will *undo*
them back to the state they were in before entering insert mode.

----------------------------------------------------------------------------

-> The vi Editor <-
===================

There are shortcuts for moving around faster in normal mode.

`^` moves to the beginning of a line
`$` moves to the end of the line
`w` will take you forward one WORD.
`b` will take you BACK one word.
`e` will take you to the END of the current or next word.
`gg` will take you to the beginning of the file.
`G` will take you to the end of the file.

----------------------------------------------------------------------------

-> The vi Editor <-
===================

These shortcuts are particularly useful for selecting text, which is
done in *visual mode*, engaged by pressing `v` while in normal mode.

Once in visual mode, using the same shortcuts one uses to navigate
text in normal mode will move the cursor through the text highlighting
regions as it passes.

Pressing `x` or `d` while text is selected will delete it.
Pressing `c` will delete the text and place you in *insert mode* where
the text was removed.

Perhaps unsurprisingly, pressing `ESC` will end visual mode and take
you back to normal mode.

----------------------------------------------------------------------------

-> The vi Editor <-
===================

Any text deleted by the use of `x`, `d`, or `c` is copied to a *yank
register*, the modern equivalent of which would be called a
*clipboard*. The text can be inserted back into the document by
pressing `p` while in normal mode. Text can be *yanked*, that is put
in the yank register by pressing `y` with the text selected.

Additionally, whole lines can be deleted or yanked.

`yy` will yank a whole line.
`dd` will delete a whole line.

These lines can then be placed back into the text with `p` (paste) or
`P` (paste behind).

----------------------------------------------------------------------------

-> The vi Editor <-
===================

Similarly, there are shortcuts to enter insert mode.

`a` will put you in insert mode *after* the cursor.
`A` will enter insert mode with the cursor at the end of the line.
`I` will enter insert mode with the cursor at the beginning of the line.
`o` will *open* a line below the current line, in insert mode.
`O` will open a line above the current line, in insert mode.

----------------------------------------------------------------------------

-> The vi Editor <-
===================

One of the most powerful features of `vi` is its search and replace
mechanism, which are powered by *regular expressions*.

`/` will let you start entering a search command. Then you can type in
your search term, such as `Detroit` to have the command
`/Detroit`. Pressing `n` in normal mode after a search will jump to
the next search result.

By learning *regular expressions*, we can create
much more powerful search patterns. However, you'll have to wait until
session 3 for that material.

----------------------------------------------------------------------------

-> The vi Editor <-
===================

There are a few more command-line mode commands that will be
useful. One of them is `set`, which allows us to turn on and off
options such as `number`, which turns on line numbers.

`:set number` will turn on line numbers.
`:set nonumber` will turn off line numbers.

`:set ruler` will turn on the position indicator.
`:set noruler` will turn off the position indicator.

`:set all` will show a rather extensive list of current settings.

The `:w` command is short for *write*, and is used to save files.
The `:q` command is short for *quit* and exits the editor.

----------------------------------------------------------------------------

-> Exercise <-
==============

Let's explore the `vi` editor.

----------------------------------------------------------------------------

-> Text display tools with vi key bindings <-
============================================

One of the reasons to go over navigating in `vi` so extensively is
that it is not the only tool to use these key bindings. The `less`
text display tool uses the same key bindings to navigate as `vi`'s
normal mode. This is the same text display tool used to display *the
manual*.

`less wifi.log`

`man date`

----------------------------------------------------------------------------

-> RTFM <-
==========

Since we're on the subject, Unix systems generally include an
extensive *manual* for nearly every command included with the
operating system.

To get information on a specific command that you know, pass it as an
argument to `man`.

`man date`

Commands and their descriptions are searchable through the `apropos`
command.

`apropos echo`

    builtin(1), echo(1), until(1), wait(1)  - shell built-in commands
    echo(1)                  - write arguments to the standard output
    lessecho(1)              - expand metacharacters

----------------------------------------------------------------------------

-> RTFM <-
==========

After using `apropos` or in the "SEE ALSO" section of a man page, you
may notice a *section* number after the entry for a command. If you
want to reference a specific a specific section number, provide it as
an argument to man before the command you are looking for the entry
for.

`man 1 echo`
