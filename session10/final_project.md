DL Unix Fundamentals Final Project
==================================

For your final project, you will be writing a script or set of scripts
to catalog things similar to
the [rolo](https://en.wikipedia.org/wiki/Rolodex) program presented
throughout the reading material. What your script catalogs is up to
you. Vinyl records, trading cards, contacts' email addresses or
kitchen cabinet inventories ­ the choice is yours.

Requirements
------------

1. Your program should store records for your catalog in a plain-text
   file (hereby, "the catalog file"). The format of this file is up to
   you based on the needs of the kinds of records you are storing, but
   use your best judgment based on your experience with the Unix text
   tools as discussed in the coursework.
2. By default, the catalog file should be stored in the user's home
   directory. Your program should check for the existence of a
   variable `CATALOG_LOCATION`, and if it exists, should use the path
   stored in that variable's value as the location of the catalog file.
3. If your program is called with arguments, it should perform a
   search for records matching the arguments and print the resulting
   records.
4. Records in your catalog should contain at least 3 fields and when
   displayed, should show on at least 2 lines. One possible format
   for a catalog of favorite pizza toppings might be:

        Green Peppers    Rating: 5/5
        Goes with: Onions, Mushrooms, Extra Cheese
        Mushrooms        Rating: 5/5
        Goes with: Onions, Green Peppers, Extra Cheese
        Pineapple        Rating: 2/5
        Goes with: basically just Ham

   The fields described here are the topping's name, the rating, and
   a user's comment about what other toppings it goes well with.
5. If called with no arguments, your program should display an
   interactive menu. This menu should offer options to search, add,
   remove, or change records in your catalog, as well as an option to
   display all of the records currently stored in the catalog and
   finally an option to exit the program.
6. When a user selects the option to search for records, they should
   be prompted for a search term. If the search term is empty, print a
   message to the user that the search has been canceled and return to
   the main menu. Otherwise, execute the search and display any
   matching results.
7. When a user selects the option to add records to the catalog, they
   should be presented with a prompt which asks them to enter each
   field required to make a record. The record should then be added to
   the catalog. After a record is added, prompt the user whether
   they'd like to add another record. If they answer affirmatively,
   repeat the process for adding a record. If they answer negatively,
   return to the main menu.
8. When a user selects the option to remove a record, they should be
   prompted for a search term. If the search term is empty, print a
   message to the user that the removal has been canceled and return to
   the main menu. If the search term is not empty, display matches
   from the catalog one at a time, prompting the user whether to
   remove that record or not. If the user answers in the negative,
   continue to display match results until the user answers
   affirmatively or until all matches are exhausted. If the user
   answers affirmatively, remove the record from the catalog and
   return to the main menu.
9. When a user selects the option to change a record, they should be
   prompted for a search term. If the search term is empty, print a
   message to the user that the change has been canceled and return to
   the main menu. If the search term is not empty, display matches
   from the catalog one at a time, prompting the user whether to
   change that record or not. If the user answers in the negative,
   continue to display match results until the user answers
   affirmatively or until all matches are exhausted. If the user
   answers affirmatively, write the record to a file and launch an
   editor for the user to edit it. Once the user exits the editor,
   modify the catalog to include the changed record. The user should
   be able to set their editor of choice by exporting a variable
   `EDITOR` containing the command to run for editing files.
10. When a user selects the option to display all the records, all of
    the records should be printed to standard output in an easily
    readable format.
11. When the user selects the option to exit the program, exit with a
    status of 0.
12. Your program should trap signal 2 (Ctrl-C) when running
    interactively to prevent the program from exiting. Rather, if the user
    presses Ctrl-C, have your program re-display the top-level menu.
13. If the catalog file does not exist and the program is running in
    interactive mode, display a prompt to the user before the main menu
    asking whether they would like the file created for them. If they
    answer negatively, exit with a status code of 1. If they answer
    positively, attempt to create the file, and if the file could not be
    created, exit with a status code of 1. If your program is running
    via command-line arguments or called with options (that is, not
    interactively) and the catalog file does not exist, exit with a
    status code of 1.
14. Your program should allow adding records without the use of an
    interactive shell to facilitate interaction with continuous
    integration or other automation. If your program is called from
    the command-line with the `-a` option and no other options, it
    should display a usage message on how to add records via a
    combination of options and/or arguments. What those other command
    line options and/or arguments are is up to you, so feel free to
    choose options that are fitting to your program. For example, a
    program that catalogs favorite pizza toppings might include:

        toppings -a "Onions" -r 4 -c "Mushrooms, Olives, Green Peppers"

15. Any input that would break the formatting of your catalog file
    should not be accepted. Try to handle escaping or quoting any
    input that would corrupt the formatting of your catalog file, but
    if necessary, simply display a message stating that the user
    cannot enter that input again and allow them to return to the main
    menu or exit if running outside of an interactive environment.


Scoring
-------

Each of the above 15 requirements will be assessed with a point value:

* 2 points - This requirement was completed
* 1 point - This requirement was partially completed or did not function
as outlined.
* 0 points - This requirement was not completed.

A score of 15 will be considered passing.
