# Linux Command Line Basics

## Get Into The Shell
1. `curl` followed by url is used to download stuff from the web.
1. Terminal - is a program that draws text in a window, and lets you type things in on a keyboard. Technically, it's called a terminal emulator since it acts like one of those old school hardware terminals.
1. Shell - Terminal itself doesn't know what to do with the input, it needs another program to do that. In this case that program is the Shell. When you type things in the terminal just sends what you type to that separate program. When you press Enter, the shell interprets what you wrote as a command, figures out what program you want to run, runs it and sends the output back to the terminal so you can see it.
1. You can use terminal without a shell, with a lot of terminal programs you can tell it what program to run. The default is a shell, but you can also have it run say, the Python interpreter instead.
1. There are a lot of different shell programs that you can choose from. The default one on most Linux systems, and on the Mac, is called GNU Bash. There are others called TCSH, KSH and Seashell.
1. You can also write shell commands into a file and arrange for your computer to run the sehll program on that file. This is called shell scripting.
1. Command line arguments - e.g. `git status` where `status` is the CL argument.
1. Example commands:
	1. `date` - tells time
	1. `expr 2 + 2` - prints 4
	1. `echo You rock` - prints You rock
	1. `uname` - prints Linux (OS's name)
	1. `hostname` - prints vagrant-ubuntu-trusty-32 (computer's name)
	1. `host udacity.com` - prints udacity info (IP addresses and stuff)
	1. `bash --version` - prints version of OS
	1. `history` - list down all the commands typed
	1. `uptime` - prints user uptime info
1. Most commands you run in the Shell are actually whole programs that are started up as separate processes on your computer.
1. Functions are use to organize programs. Shell commands are used to run programs. Although they are quite similar in a sense that they can be called and take arguments.

## Shell Commands
1. `.pem` is a crypto key file - the keys for ssh for SSL web servers are usually in pem files. PEM stands for privary enhanced mail.
1. `.sh` is a text file with shell commands.
1. `cat file1.txt file2.txt` short for concatenate - concatenate text files and output on the terminal.
1. Use Tab for autocomplete.
1. `wc` - does word counts. Prints lines, words and bytes are in a file.
1. `diff` - compares files and shows you how they differ.
1. `man package_name` usually returns you the manual and shows all the commands within the package.
1. `.stuff` - files thats start with a dot will not be shown when `ls` is run by default. Because these files are often used for caching and configuration and other things that you don't normally care about. So the shell will hide them by default because they're not usually interesting. To display them with `ls`, use `ls -a` or `ls --all`
1. `ls -l` for long listing format. 1st argument of each line tells you if it's a directory (starts with `d`) or a file (starts with `-`).
1. `rm -rf` remove stuff without asking. `-r` means recursive. `-f` means force.
1. Line based commands - takes over terminal as long as they're running, then get the shell back when the program exits. E.g. interupted by `ctrl c` or `ctrl d`.
1. Common design in Linux programs where a program will read from what's called the standard input or `stdin` and write to what's called standard output or `stdout`. This allows programs to be chained tohether on to a pipeline. When you run a program like this in a terminal, it will read from your keyboard input and write back to the terminal screen and often when your input is done you want to send it an end of file character which you do by typing `ctrl + d`. E.g. `sort` command.
1. `ctrl + d` stands for end of file.
1. `less` - use to display text one page at a time. Its used by `man`. Can be used for `.txt` files.
1. [Less Cheat Sheet](http://sheet.shiar.nl/less).
1. Editing files in the terminal - terminal based text editor e.g. vim, emacs, joe, nano etc. E.g. `nano stuff.txt`

## The Linux Filesystem
1. The Filesystem tree - stores various kinds of objects most commonly files and directories.
1. Cannot use forward slash `/` for file names.
1. Precede characters with `\` for special characters such as `!$#()[]`.
1. E.g. a file called Great Filename! to be quoted as 'Great Filename' or escaped as Great\ Filename\!
1. Shell, and every other program for that matter, has a working directory.
1. `ls` with no arguments will list files in the Shell's current working directory.
1. `pwd` - print working directory.
1. Absolute Paths - from the root
1. Relative Paths - relative to current directory.
1. `.` is the current working directory.
1. `~/directory` - `~` is an abbreviation for your own home directory.
1. `cd` by itself takes you to home directory.
1. `mv` to move or rename. Usage: `mv source destination` 
1. `cp` to copy.
1. `mkdir` to make a new directory.
1. `rmdir` to remove empty directory.
1. `rm -f directory` to remove directory and everything within it.
1. Globbing - matching files by name in the Unix Shell. E.g. `ls *app*`, `ls app.{css,html}` returns `app.css app.html`
	1. `ls bea?.png` returns `bear.png bean.png` but not `beast.png`
	1. `ls be[aeiou]r.png` returns `bear.png beer.png`
1. `echo Short names: ?` prints "Short names:" followed by all the one-character filenames in the current directory.
