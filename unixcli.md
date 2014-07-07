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
- Stream redirection
    - \> - Redirect to new file or overwrite existing
    - \>\> - Redirect to new file or append to existing
    - < - Read from existing file and use as input to command
    - tee - Copies stdin contents to stdout and any files specified

## [Getting help](#help)

- man (man 3 printf)
- apropos
- whatis

## [Searching in files](#searchinfiles)

- grep (and friends)

## [Searching for files](#searchforfiles)

- find
- locate
- whereis
- tree

## [Manipulating files](#manipulating)

- cut
- tr
- paste
- join
- sort
- uniq
- expand, unexpand

## [Network tools](#network)

- ping
- curl
- wget

## [Miscellaneous](#miscellaneous)

- single quotes vs double quotes
- alias
- functions
- history
- ctrl-r (history search)
- xargs

## <a name="printing">Printing to stdout</a>

In case you're not familiar with stdout, it is explained in the section, [Streams, pipes, and redirection](#streams), but in the mean time, and for the purpose of this section, stdout is the shell/terminal in which you type commands. So if a program sends "hello" to stdout, it means it's displaying "hello" in the terminal for you to read.

### echo
Just echos whatever you type to stdout.

```
jeff$ echo 'hi there'
hi there
jeff$ echo "this is \n \n still one line"
this is \n \n still one line
```

Note that by default `echo` does not escape anything for you.

_Useful flags_

`-e` escapes common backslash sequences for you:

```
jeff$ echo -e "one\t\t\ttwo\n\nthree"
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
jeff$ head -n 2 numbers.txt
one
two
```

You can also just do `head -[number] [filename]` to get the same effect:

```
jeff$ head -2 numbers.txt
one
two
```

You can also pass in a bunch of files at once and `head` will treat them in a sensible way:

```
jeff$ head -2 *.txt
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
jeff$ tail -2 numbers.txt
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
- stderr: standard error output, usually hooked up to the terminal screen
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
jeff$ cat numbers.txt | grep 'teen'
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
jeff$ cat numbers.txt | grep 'teen' | head -3 | grep 'i'
thirteen
fifteen
```

This is obviously a toy example, but the point here is to show how to use a pipe to hook up multiple commands to get a job done. What would otherwise have a been a few minutes to write a quick and dirty multi-line program in a traditional programming language just took a few seconds and a string of simple unix commands.

One more thing to say about pipes is that the programs connected by the pipe actually run in parallel. The program on the left side of the pipe will send data as soon as possible and the right side will receive as soon as possible. If there is no data generated, the right side simply waits for the input to come. This allows us to do neat things like the following (recall that `tail -f` will continue to print the new contents of a file to stdout until it's terminated via ctrl-c or otherwise):

```
jeff$ tail -f my_server.log | grep ERROR
ERROR: Oh no! Something went wrong!
ERROR: Seriously... it's all going bad, don't deploy on Fridays!
...
```

What's cool here is that we can watch a log file in real time and ignore any line that doesn't contain 'ERROR'. Again, the important bit here is that `tail` does not finish execution before grep starts filtering out things that don't contain 'ERROR'. Pipes run their left and right side programs in parallel, not one after the other.

One last thing I'd like to say is if this is your first time seeing `grep`, you don't need to `cat` a file into `grep` using a pipe. `grep` can just take a file as a normal command line argument, so the first example above can be shortened to just `grep 'teen' numbers.txt`.

### Stream Redirection

There are two types of stream redirection, output redirection, and input redirection.

**output redirection**

Output redirection is somewhat like a pipe in that it redirects the output of a program to another location, but instead of redirecting to another program, you redirect to another file. A file can certainly be a traditional file on the disk that you can save to to open later, but it can also be any unix file device, like those in /dev/* or a network socket, or whatever. If the OS treats it as a file (and you have the appropriate permissions), you redirect to/from it.

What you're able to redirect are our friends: stdout, stderr, and stdin. If you're not already familiar with streams, you can read up on them a couple section above.

Say you have a program that outputs a whole lot of useful information, and you don't want to scroll up and down your terminal to read it. Or maybe you want to email the output of a command to a friend. You can instead tell the program to store it's output to a file using a redirect.

The `ls -lh` command prints the contents of my current directory:

```
jeff$ ls -lh
total 56
-rw-r--r--  1 jeff  jeff    12B May 20 11:13 README.md
-rw-r--r--  1 jeff  jeff   132B May 20 13:55 numbers.txt
-rw-r--r--  1 jeff  jeff   134B May 20 13:58 numeros.txt
-rw-r--r--@ 1 jeff  jeff    12K May 25 20:30 unixcli.md
```

To save that output to a file instead, I use the `>` redirection symbol:

```
jeff$ ls -lh > output.txt
jeff$ cat output.txt
total 56
-rw-r--r--  1 jeff  jeff    12B May 20 11:13 README.md
-rw-r--r--  1 jeff  jeff   132B May 20 13:55 numbers.txt
-rw-r--r--  1 jeff  jeff   134B May 20 13:58 numeros.txt
-rw-r--r--@ 1 jeff  jeff    12K May 25 20:30 unixcli.md
```

Similar to the pipe, the `>` tells the shell to intercept what was sent to stdout, and instead send it to the file after the `>` symbol, in this case, the file *output.txt*. Since *output.txt* didn't yet exist, it is created. Note well, that if *output.txt* already exists (and you have permission), it will be overwritten. Again, the existing contents of output.txt would be **deleted permanently and replaced** with the new output. If you want to append to an existing file, you should use `>>` instead. The `>>` will also create the file if it doesn't already exist.

Note that this will only redirect what was sent to stdout. Anything sent to stderr goes along it's normal path. To redirect stderr, we need to use `2>` instead, which looks odd at first:

```
jeff$ ls does_not_exist/
ls: does_not_exist/: No such file or directory
jeff$ ls does_not_exist/ 2> error.txt
jeff$ cat error.txt
ls: does_not_exist/: No such file or directory
```

Remember, that last line isn't an error, it's just the printout of the contents of error.txt, which stored the redirected error output from the previous command.

So what's that `2>` all about? The short version is that the streams stdin, stdout, and stderr each map to file descriptors of 0, 1, and 2, respectively. If you don't know about file descriptors, suffice it to say they are unique numbers assigned to files by the operating system to be able to communicate with them. The `>` can take an argument of sorts, which is placed right before the `>` itself, which is a file descriptor to redirect. By default, it's 1, which is the file descriptor number for stdout. In this case, since we want to redirect stderr, I use `2>` to say "redirect file descriptor 2 (stderr)". The "default" for redirected file descriptor is 1, so saying `>` is the same as `1>`.

To make things more exciting, we can redirect stdout and stderr to different files in the same line (the `.` in `ls -lh . does_not_exist/` just means current directory, so I'm asking it to display the contents of both the current directory, as well as another directory that does not exist):

```
jeff$ ls -lh . does_not_exist/
ls: does_not_exist/: No such file or directory
.:
total 72
-rw-r--r--  1 jeff  jeff    12B May 20 11:13 README.md
-rw-r--r--  1 jeff  jeff    47B May 25 21:05 bad.txt
-rw-r--r--  1 jeff  jeff   476B May 25 21:05 good.txt
-rw-r--r--  1 jeff  jeff   132B May 20 13:55 numbers.txt
-rw-r--r--  1 jeff  jeff   134B May 20 13:58 numeros.txt
-rw-r--r--@ 1 jeff  jeff    15K May 25 21:05 unixcli.md
jeff$ ls -lh . does_not_exist/ >good.txt 2>bad.txt
jeff$ cat good.txt
.:
total 64
-rw-r--r--  1 jeff  jeff    12B May 20 11:13 README.md
-rw-r--r--  1 jeff  jeff    47B May 25 21:05 bad.txt
-rw-r--r--  1 jeff  jeff     0B May 25 21:05 good.txt
-rw-r--r--  1 jeff  jeff   132B May 20 13:55 numbers.txt
-rw-r--r--  1 jeff  jeff   134B May 20 13:58 numeros.txt
-rw-r--r--@ 1 jeff  jeff    15K May 25 21:05 unixcli.md
jeff$ cat bad.txt
ls: does_not_exist/: No such file or directory
```

As expected, we can see in each of good.txt and bad.txt, the respective succesful output text as well as the error output text.

So what if you want to redirect both stdout and stderr to the same file now? If you tried what seems to be a logical command, you'd be disappointed and possibly confused. The following will not produce a file with both stdout and stderr's output:

```
jeff$ ls -lh . does_not_exist/ >all.txt 2>all.txt
jeff$ cat all.txt
.:
total 72
-rw-r--r--  1 jeff  jeff    12B May 20 11:13 README.md
-rw-r--r--  1 jeff  jeff    47B May 25 21:11 all.txt
-rw-r--r--  1 jeff  jeff   132B May 20 13:55 numbers.txt
-rw-r--r--  1 jeff  jeff   134B May 20 13:58 numeros.txt
-rw-r--r--@ 1 jeff  jeff    16K May 25 21:11 unixcli.md
```

The stderr output is not there! Remember that redirects will overwrite whatever file was there before. So what's happening here is that `2>` wrote it's stderr output to the file (probably a bit quicker since it's not buffered), then `>` came along and wrote right over it! The fact is we cannot redirect both stdout and stderr to the same file without one stomping over the other! What to do?

The fix is rather clever. The trick is to redirect stderr to stdout! Instead of having two redirects write to the same file, only the stdout redirect will write to the file, and stderr gets redirected to stdout, so stderr is indirectly written to the file through stdout. The notation is a bit odd at first, but it makes sense if you understand what's going on. Keeping in mind that the file descriptors for stdout and stderr are 1 and 2 respectively, here's how you redirect both to *all.txt*:

```
jeff$ ls -lh . does_not_exist/ >all.txt 2>&1
jeff $ cat all.txt
ls: does_not_exist/: No such file or directory
.:
total 80
-rw-r--r--  1 jeff  jeff    12B May 20 11:13 README.md
-rw-r--r--  1 jeff  jeff    47B May 25 21:22 all.txt
-rw-r--r--  1 jeff  jeff   476B May 25 21:21 good.txt
-rw-r--r--  1 jeff  jeff   132B May 20 13:55 numbers.txt
-rw-r--r--  1 jeff  jeff   134B May 20 13:58 numeros.txt
-rw-r--r--@ 1 jeff  jeff    17K May 25 21:22 unixcli.md
```

The last odd bit of syntax there is the `&` in `2>&1`. The `&` is required to tell the shell you are referring to the file descriptor 1, rather than the filename *1*. `2>1` (without the `&` would actually create a regular file with the name *1*, which is perfectly legal).

Since that's so ugly, we have a bit of special syntax which is a shortcut for `>all.txt 2>&1`. Instead, you can just type `&>all.txt`. So, to redirect both stdout and stderr to *all.txt*, you would just type `command &>all.txt`.

**Input Redirection**

Just as you can control where the output ends up with redirection, you can control where the input comes from. The input redirection symbol is the `<` symbol, and is also placed to the right of the command to accept the input. The following example is a modification of the one in the section on pipes, this time using input redirection in place of `cat` and the first pipe. We again have our existing file *numbers.txt* and we want to see only those lines that contain an 'i' amongst the first three 'teen' words:

```
jeff$ grep 'teen' <numbers.txt | head -3 | grep 'i'
thirteen
fifteen
```

Just for fun, you can also verify that 0 is the file descriptor for stdin here, as well as verify that it is buffered on a line by line basis, with the following (hit ctrl-c to exit):

```
jeff$ cat <&0
hello    <- type 'hello' hit enter
hello
echo     <- type 'echo' hit enter
echo
^C
```

**Input and Output Redirection**

If you want to redirect both the input and output for the same command, it looks like this:

```
jeff$ grep 'teen' <numbers.txt &>teens.txt
```

**Tee**

`tee`, when combined with pipes, redirects stdout (like `>`) to a file or files, but also sends a copy to stdout as well. Like a T shaped water pipe. This is useful for when you want to save a log of stdout, but still want it to show up on the screen. If you want to append to rather than delete an existing file, you should use the `-a` flag. Example:

```
jeff$ grep 'teen' numbers.txt | tee teens.txt
```

This will print the teens to the screen as well as save them in *teens.txt*. Note that this only works for stdout. If you want to save both to the same file, you can redirect stderr to stdout, then pipe as normal:

```
jeff$ ls -lh . does_not_exist/ 2>&1 | tee all.txt
```

If you need to tee stdout and stderr to separate files, the explanation is a bit more complicated, so instead I leave you with a sample command, and the term "process substitution" for which to google/bing/internet-search:

```
jeff$ ls -lh . does_not_exist/ > >(tee stdout.txt) 2> >(tee stderr.txt >&2)
```


## <a name="help">Getting help</a>

### man
Short for manual, man gives access to a command's manual, often called it's manpage. Here you can find a basic description of a command, along with flags it accepts, and sometimes useful examples.  

Example: `man printf` will describe how to use `printf` to write to stdout. Useful, for example, if a simple `echo` isn't enough for the task at hand.

_Useful flags_

`-a`: man pages are divided into multiple sections (See list of common [man page sections on Wikipedia](http://en.wikipedia.org/wiki/Man_page#Manual_sections)). Use `-a` if you want to see all available sections of a manpage for a tool: `man -a printf`

`man [section number] [command]`: If you already know you want to see a specific section of the manpage for a command, you can just pull it up: `man 3 printf`. By default, the lowest number section is pulled up.

### apropos

`apropos` will search for keywords in a database of descriptions for commands that might be useful to your search terms. Some examples:

```
jeff$ apropos pdf
pdfroff(1)               - create PDF documents using groff pdfmark Q \$3\$1\$2 . nohy \$*
snmpdf(1)                - display disk space usage on a network entity via SNMP
jeff$ apropos ruby
gem(1)                   - RubyGems program
irb(1), erb(1), ri(1), rdoc(1), testrb(1) - Ruby helper programs
ruby(1)                  - Interpreted object-oriented scripting language
```

### whatis

`whatis` will compare search terms against a database of descriptions of the commands available on your system and return those it is able to find a match for. Often times it returns either too much or too little, but it's a handy tool if you need to do a quick lookup for a new command.

```
jeff$ whatis apropos
apropos(1)               - search the whatis database for strings
jeff$ whatis ps
img-ps(n)                - Img, Adobe PostScript Format (ps)
ps(1)                    - process status
jeff$ whatis dd
dd(1)                    - convert and copy a file
```

## <a name="searchinfiles">Searching in files</a>

### grep and friends

`grep` is one of my favorite command line tools. `grep` revolves around the concept of regular expressions. If you're not familiar with regular expressions, suffice it to say it's a mini-language for expressing patterns of characters. This grep intro covers simple `grep` usage, so you'll be ok without regular expression knowledge. Just know grep can be used for much more complicated pattern matching tasks than those shown here.

`grep` fun fact: The name comes from it's original existance as a command within an older program called 'ed', which allowed you to find all matches for a regular expression with the command 'g/re/p'. This utility was useful enough to be pulled out into it's own little program and called 'grep'.

The entire purpose of `grep` is to take its input line by line and compare each of those lines to a supplied pattern. If the pattern is found anywhere in the line, the line is sent to stdout. In a sense, `grep` will remove any lines that do **not** match the supplied pattern.

Here I want to pull out the teen numbers from numbers.txt:

```
jeff$ grep 'teen' numbers.txt
thirteen
fourteen
fifteen
sixteen
seventeen
eighteen
nineteen
```

`grep` is extra useful when combined with pipes, to filter out lines from another command which might produce a lot of output:

```
jeff$ ps x | grep 'firefox'
37261   ??  R     79:28.43 /Applications/Firefox.app/Contents/MacOS/firefox -psn_0_25745548
41829 s001  R+     0:00.00 grep --color=auto firefox
```

`grep` can be used to search through logfiles for errors, or other odd behavior. I recently decided to grep for the pattern *POST* on my website (which should only be receiving GET requests) and found that someone had been sending malicious POST requests to my box.

_Useful flags_

`-v` Inverts the results of the pattern matching. In other words, it only prints lines that **do not** match the pattern.

`-i` Is for case insensitive pattern matching. `grep bob` will match "bob" as well as "BOB" and "bOb".

`-n` Print line numbers along with the matched lines.

`--color=auto` Highlights matches in your terminal, if possible. I have an alias set up in my profile so that this is the default when I type `grep` (See the `alias` command under [Miscellaneous](#miscellaneous)).

`-[number]` Prints [number] lines before and after the matched line, as well as the line itself. This is useful if you want some context around your match.

`-A` allows you to specify a number of lines to print after the matched line.

`-B` allows you to specify a number of lines to print before the matched line.

_egrep, fgrep, zgrep, zegrep, zfgrep_

Please see `man grep` for details, but here's a run down of grep's family of commands:

`egrep`: "extended grep". grep itself is fairly limited in the subset of regular expression syntax it supports. `egrep` supports a few more features of regular expressions you might be used to in a full-on programming language.

`fgrep`: "fast grep" or "fixed grep". For searching through large files with simple fixed patterns to match (no asterisks, etc), `fgrep` might speed up your command as the command knows ahead of time the pattern is simple.

`zgrep`, `zegrep`, `zfgrep`: Similar to the above commands, but allow you to search through compressed files. It is able to look through .Z files as well as newer .gz files. Very useful for looking through old log files that automatically log rotated in a compressed state.

## <a name="searchforfiles">Searching for files</a>

### find

`find` is great for when you need to quickly find a file or directory, especially if you've narrowed it down to a subfolder. `find` has a large number of options and is fairly powerful in itself, so I'm only convering one simple yet common use here:

`find . | grep 'searchterm'`

`find .` on itself lists all filenames in your current directory (recall that the '.' is current directory) and, recursively, subdirectories:

```
jeff$ find .
.
./a.txt
./b.txt
./stuff
./stuff/coolStuff
./stuff/coolStuff/bikes.txt
./stuff/coolStuff/cars.txt
./stuff/funStuff
./stuff/funStuff/friends.txt
./stuff/funStuff/rockClimbing.txt
```

If you want to find a specific file or set of files, you pipe the output into grep and match a pattern in that way:

```
jeff$ find . | grep fun
./stuff/funStuff
./stuff/funStuff/friends.txt
./stuff/funStuff/rockClimbing.txt
```

Like I mentioned, `find` is very powerful, and this is just one tiny use of it. There are entire tutorials to be found online. It is capable of filtering based on various file attributes, as well as performing actions on the found files, such as deleting, or even arbitrary commands (see -exec option in man page).

### locate

### whereis

### tree

## <a name="manipulating">Manipulating files</a>

### cut

### tr

### paste

### join

### sort

### uniq

### expand, unexpand

## <a name="network">Network tools</a>

### ping
### curl
### wget

## <a name="miscellaneous">Miscellaneous</a>

### Single vs Double Quotes
Double quotes will interpret the $ as the beginning of an environment variable. Single quotes will treat the $ sign as a literal $.

```
jeff$ echo -e 'My shell is $SHELL'
My shell is $SHELL
jeff$ echo "My shell is $SHELL"
My shell is /bin/bash
```

### alias

### functions

### history

### ctrl-r (history search)

### xargs
