# Unix Command Line

## Files used in examples

There are a number of files used in these examples, feel free to take a peek at them:

- [numbers.txt](numbers.txt)
- [numeros.txt](numeros.txt)

## [Printing to stdout](#printing)

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

## <a name="printing">Printing to stdout</a>

In case you're not familiar with stdout, it is explained in the section, [Streams, pipes, and redirection](#streams), but in the mean time, and for the purpose of this section, stdout is the shell/terminal in which you type commands. So if a program sends "hello" to stdout, it means it's displaying "hello" in the terminal for you to read.

### echo
Just echos whatever you type to stdout.

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

Note: `-e` doesn't show up in my man page on osx 10.8.5, but still works.

### cat
Con-**cat**-enate files. This is commonly used to print an entire text file to stdout but this tool also allows you to concatenate multiple files together, which is where the name comes from. For example:

`cat numbers.txt numeros.txt` will print the entire contents of numbers.txt followed by numeros.txt to stdout.  
`cat *.log` will print the contents of all .log files in the current directory

_Useful flags_

`-n` prints line numbers with each line: `cat -n numbers.txt`

### head
Similar to `cat`, but only prints up to the first n available lines of a file to stdout. By default prints up to the first 10 lines. This is useful to just see the headers of a csv files, or just to see what the contents of a data file look like.

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

`-f`: Follow. This prints the last lines of a file, but continues to listen for any new lines appended to the file. This flag is extremely useful for monitoring logs in real time for running services. `tail` will continue to print the newly added contents of the file to stdout until you hit ctrl-C or otherwise kill the `tail` process.

### less

A way to look through a text file without dumping all of it to the screen at once. This is useful for files that are too large to fit in a single screen height. Type 'q' to exit. Basic emacs navigation controls work inside less (ctrl-n, ctrl-p, etc).

## <a name="streams">Streams, pipes, and redirection</a>

### Streams (stdin, stdout, stderr)
Unix streams are a mechanism which allow you to send bytes of data from one "thing" to another. Examples of these "things" are your keyboard, your terminal window, a file on your disk, a network socket, a serial port, or any of a number of special "things" that live on your machine, such as the *null device*, commonly known as /dev/null (try running `man null` for more info).

There are three common streams that by convention are hooked up to certain devices by default:

- stdout: standard output, usually hooked up to the terminal screen
- stderr: standard error, usually hooked up to the terminal screen
- stdin: standard input, usually hooked up to the keyboard

You can think of these as specialized dynamic files that a program can access. Unlike writing to a normal file, however, instead of sending the characters or bytes to be written to disk for storage, writing to stdout sends the bytes to be displayed in your terminal, which show up on your monitor for your eyes to read (this is the default behavior). In fact, stdout, stderr, and stdin are treated exactly the same as files by the programs that use them.

**stdout** is, by convention, where you send normal output that you want the program's user to read.  
**stderr** is, by convention, where you send exceptional output (like error messages) that you want your program's user to read.  
**stdin** is, by convention, where the program's user puts information that the program will use as input. The default and most common example is the keyboard, but this can also be a file on disk, a network socket, or any kind of readable file-like device on your computer.

I take care to say *by convention* because there is really nothing special about the way a program interacts with these streams. There is no mechanism in place keeping a program from writing error output to stdout for example, and in some cases, this will occur in "real" programs.

A practical difference between stdout and stderr is that stderr is generally not buffered. That is, as soon as a character is written to stderr, it will be sent to it's destination. Stdout is commonly buffered on a line by line basis. Read the documentation for your particular shell or program if this is a relevant detail for your use (try running `man fd`, the fd is for file descriptor).

### Pipe ( | )

The pipe allows you to redirect or repurpose the output of one program as the input of another. Recall from the previous section on streams that in order to write to the terminal screen, a program will write data to stdout. A pipe will grab the data on stdout that was otherwise headed to the terminal screen and instead feed it to the second program, supplying it as input.

Let's see a simple example here. If you are not familiar with the `grep` tool, it is explained later in the [Searching in files](#searchinfiles) section. For now, you can consider grep to be a tool that filters out lines in a file that do not contain the word provided, and keeps only those that do. So here I am supplying *numbers.txt* to `cat`, which would normally just print the entire contents to the screen. However, by using a pipe right after it, I am telling my shell to intercept that output and instead provide it to `grep 'teen'` instead. `grep` then does the work of checking each line in *numbers.txt* to see if *teen* is found. If it is found, `grep` in turn sends that line to stdout, it it is not found, it just drops it. Now the normal things happens with `grep`'s stdout, since there is no pipe, it just sends the characters to the screen.

```
> cat numbers.txt | grep 'teen'
thirteen
fourteen
fifteen
sixteen
seventeen
eighteen
nineteen
```

You can stack as many pipes on top of each other as you like. Here I first grab only the teen numbers (`grep 'teen'`). Then, of those, I keep only the first three lines (`head -3`). Lastly, of this first three teens, I keep only the ones that contain the letter 'i' in them (`grep 'i'`).

```
> cat numbers.txt | grep 'teen' | head -3 | grep 'i'
thirteen
fifteen
```

This is obviously a toy example, but the point here is to show how to use a pipe to hook up multiple commands to get a job done. What would otherwise have a been a few minutes to write a quick and dirty multi-line program in a traditional programming language just took a few seconds and a string of simple unix commands.

One more thing to say about pipes is that the programs connected by the pipe actually run in parallel. The program on the left side of the pipe will send data as soon as possible and the right side will receive as soon as possible. If there is no data generated, the right side simply waits for the input to come. This allows us to do neat things like the following (recall that `tail -f` will continue to print the new contents of a file to stdout until it's terminated via ctrl-c or otherwise):

```
> tail -f my_server.log | grep ERROR
ERROR: Oh no! Something went wrong!
ERROR: Seriously... it's all going bad, don't deploy on Fridays!
...
```

What's cool here is that we can watch a log file in real time and ignore any line that doesn't contain 'ERROR'. Again, the important bit here is that `tail` does not finish execution before grep starts filtering out things that don't contain 'ERROR'. Pipes run their left and right side programs in parallel, not one after the other.

One last thing I'd like to say is if this is your first time seeing `grep`, you don't need to `cat` a file into `grep` using a pipe. `grep` can just take a file as a normal command line argument, so the first example above can be shortened to just `grep 'teen' numbers.txt`.

### Redirection

**To new file or overwrite existing**

**To new file or append to existing**

**Use existing file as input to command**

**Tee - Copies stdin contents to stdout as well as any files specified**


- \> - Redirect to new file or overwrite existing
- \>\> - Redirect to new file or append to existing
- < - Read from existing file and use as input to command
- tee - Copies stdin contents to stdout and any files specified

## <a name="help">Getting help</a>

### man
Short for manual, man gives access to a command's manual, often called it's manpage. Here you can find a basic description of a command, along with flags it accepts, and sometimes useful examples.  

Example: `man printf` will describe how to use `printf` to write to stdout. Useful, for example, if a simple `echo` isn't enough for the task at hand.

_Useful flags_

`-a`: man pages are divided into multiple sections (See list of common [man page sections on Wikipedia](http://en.wikipedia.org/wiki/Man_page#Manual_sections)). Use `-a` if you want to see all available sections of a manpage for a tool: `man -a printf`

`man [section number] [command]`: If you already know you want to see a specific section of the manpage for a command, you can just pull it up: `man 3 printf`. By default, the lowest number section is pulled up.

## <a name="searchinfiles">Searching in files</a>

## <a name="searchforfiles">Searching for files</a>

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
