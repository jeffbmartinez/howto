# Unix Command Line

## Files used in examples

There are a number of files used in these examples, feel free to take a peek at them:

- [numbers.txt](numbers.txt)
- [numeros.txt](numeros.txt)

## [Printing to console (stdout)](#printing)

- echo
- cat
- head
- tail
- less

## [Streams, pipes, and redirection](#streams)

- Streams (stdin, stdout, stderr)
- pipe ( | )
- redirection
    - \> - Redirect to new file or overwrite existing
    - \>\> - Redirect to new file or append to existing
    - < - Read from existing file and use as input to command
    - tee - Copies stdin contents to stdout and any files specified

## [Getting help](#help)

- man (man 3 printf)
- whatis
- apropos

## [Searching in files](#searchinfiles)

- grep
- egrep
- fgrep
- zgrep

## [Searching for files](#searchforfiles)

- find
- whereis
- tree

## [Manipulating files](#manipulating)

- cut
- tr
- sort
- uniq

## [Network tools](#network)

- ping
- curl
- wget

## [Miscellaneous](#miscellaneous)

- single quotes vs double quotes
- alias
- history
- ctrl-r (history search)
- xargs

[jeffbmartinez.com](http://www.jeffbmartinez.com)

**bold stuff**

```
no formatting     for 
this stuff
  here
```

- lists
- lists
    - nested list
    - nested list
- lists

## <a name="printing">Printing to console (stdout)</a>

### echo
Just echos whatever you type into the console.

```
> echo 'hi there'
hi there
> echo "this is \n \n still one line"
this is \n \n still one line
```

Note that by default `echo` does not escape anything for you.

_Useful flags_

`-e` escapes common backslash sequences for you:

```
> echo -e "one\t\t\ttwo\n\nthree"
one			two

three
```

Note: `-e` doesn't show up in my man page on osx 10.8.5, but still works in the console.

### cat
Con-**cat**-enate files. This is commonly used to print an entire text file to stdout but this tool also allows you to concatenate multiple files together, which is where the name comes from. For example:

`cat numbers.txt numeros.txt` will print the entire contents of numbers.txt followed by numeros.txt to the console.  
`cat *.log` will print the contents of all .log files in the current directory

_Useful flags_

`-n` prints line numbers with each line: `cat -n numbers.txt`

### head
Similar to `cat`, but only prints up to the first n available lines of a file to the console. By default prints up to the first 10 lines. This is useful to just see the headers of a csv files, or just to see what the contents of a data file look like.

_Useful flags_

`-n`: Show the first n lines instead of the default of 10:

```
> head -n 2 numbers.txt
one
two
```

You can also just do `head -[number] [filename]` to get the same effect:

```
> head -2 numbers.txt
one
two
```

You can also pass in a bunch of files at once and `head` will treat them in a sensible way:

```
> head -2 *.txt
==> numbers.txt <==
one
two

==> numeros.txt <==
uno
dos
```

### tail
Similar to `head`, but prints the last n lines of a file of the first n lines. This is extremely useful for peeking at the last few lines of a log file for a running service.

```
> tail -2 numbers.txt
nineteen
twenty
```

_Useful flags_

`-n`: See `head`, same usage.

`-f`: Follow. This prints the last lines of a file, but continues to listen for any new lines appended to the file. This flag is extremely useful for monitoring logs in real time for running services. `tail` will continue to print the newly added contents of the file to the console until you hit ctrl-C or otherwise kill the `tail` process.

### less

A way to look through a text file without dumping all of it to the screen at once. This is useful for files that are too large to fit in a single screen height. Type 'q' to exit. Basic emacs navigation controls work inside less (ctrl-n, ctrl-p, etc).

## <a name="streams">Streams, pipes, and redirection</a>

## <a name="help">Getting help</a>

### man
Short for manual, man gives access to a command's manual, often called it's manpage. Here you can find a basic description of a command, along with flags it accepts, and sometimes useful examples.  

Example: `man printf` will describe how to use `printf` to write to stdout. Useful, for example, if a simple `echo` isn't enough for the task at hand.

_Useful flags_

`-a`: man pages are divided into multiple sections (See list of common [man page sections on Wikipedia](http://en.wikipedia.org/wiki/Man_page#Manual_sections)). Use `-a` if you want to see all available sections of a manpage for a tool: `man -a printf`

`man [section number] [command]`: If you already know you want to see a specific section of the manpage for a command, you can just pull it up: `man 3 printf`. By default, the lowest number section is pulled up.

## <a name="searchinfiles">Searching in files</a>

## <a name="searchforfiles"Searching for files</a>

## <a name="manipulating">Manipulating files</a>

## <a name="network">Network tools</a>

## <a name="miscellaneous">Miscellaneous</a>

### Single vs Double Quotes
Double quotes will interpret the $ as the beginning of an environment variable. Single quotes will treat the $ sign as a literal $.

```
> echo -e 'My shell is $SHELL'
My shell is $SHELL
> echo "My shell is $SHELL"
My shell is /bin/bash
```

#### tee
