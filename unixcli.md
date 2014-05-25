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
Similar to `cat`, but only prints the first n lines of a file to the console.

### tail

### less

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


#### tee
