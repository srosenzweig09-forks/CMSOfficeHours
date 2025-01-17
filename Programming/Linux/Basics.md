# Linux

Philosophy: **small, sharp tools**

Bash is the "**B**ourne-**a**gain **sh**ell".
It is the standard interpreter on most Linux distributions.

## Basic Commands

```bash
ls     # List files.
# Useful flags:
# -l : long list
# -h : human readable
# -t : sorted by time modified
# -r : reverse the order
# -a : show all files, even hidden ones
# -S : Sort files by size.

pwd                 # Print Working Directory. Shows you where you are.
cd <path/to/files>  # Change Directory. This is how you move around.
cd ..               # Go up a dir.
cd -                # Go back to previous dir.
mv <src> <dest>     # Move or rename a file (THIS WILL OVERWRITE <dest>).
cp <src> <dest>     # Copy source fileto destination.
mkdir <newdir>      # Make a new directory.
man -k <cmd>        # Shows you the manual for the command.

# Step it up a notch:
head <file>  # Print the last 10 lines in the file.
tail <file>  # Print the last 10 lines in the file.
head -n 2 <file>   # Print the first 2 lines in the file.
tail -n 15 <file>  # Print the last 15 lines in the file.
tail -n +7 <file>  # Print ALL lines from line 7 onward.

>   # The "redirection" operator. Sends output somewhere.
|   # The "pipe" operator. Makes one command's output another's input.

# Examples:
# Pipe the output of `ls` into the `grep` command:
ls -l | grep "Apr" > somefile.txt
command-name 2> errors.txt

# Send command1's output to out.txt and the errout to err.txt.
command1 > out.txt 2> err.txt

cmd 2>&1 | tee log.txt

scp  # Secure copy from one computer to another.
scp <source> <dest>	# the remote <source> or <dest> should be of the form: user@server:/path/to/file
scp -r 
history  # shows you all previous commands you’ve entered
more     # prints to stdout the entire file(?)
less     # prints to temporary shell, not to stdout
wc <file> # Word count. -l : count lines, -c : count bytes, -w
sort [-nN] [-r]  # usually sorts alphabetically, sort by number size with -n, -r is reverse search
uniq     # returns unique values 
diff <file1> <file2>  # see the differences between <file1> and <file2>.
# In the output, '<' indicates <file1>, '>' indicates <file2>
diff -r <dir1> <dir2> # Compares differences between all files in directories.

top -n 1 -b | grep chenguan  # See system summary and running processes; -n flag is iterations; -b is batch mode
# can grep to see a user's processes
free  [-g]  # displays the amount of free and used memory in the system; -g to make it more readable
read [-s] [-p] [<prompt>] <var> # stores user input into <var>; -s=silent text, -p=prompt becomes <prompt>
cut -d' ' -f2-4  # use whitespace as delimiter, and cut (print to screen) only fields (columns) 2 through 4
cat <file1> | tee [-a] <file2>  # tee will append the stdout to both a file and to stdout (it piplines the info into a 'T' shape)
ln -s <file> <link_name>  # creates a symbolic link (a reference) between <file> and <link_name>
# If you modify <link_name> then you WILL MODIFY <file>!
# Except for 'rm'; deleting <link_name> does NOT delete <file>
file <> FIXME
printf    # appears to just be a fancier and more reliable echo

alias				# check your aliases
alias <newalias>="<command>"	# add <newalias> to 
```

## Intermediate Commands

```bash
watch -n 10 '<cmd>'	# repeats <cmd> every 10 seconds
- default is 2 seconds
uname -a			# look at your Linux kernel architecture, server, etc.
uname -n			# find out what node you're on
ldd --version		# Check the glibc version.
env					# print all your environmental variables to stdout
gdb					# GNU DeBugger (not sure how this works yet)
basename <filepath> 	# strips <filepath> of directory part of name and suffix
- basename /usr/bin/sort 		# returns: 'sort'
- basename include/stdio.h .h	# returns: stdio
date					# prints the date
whoami      # Prints your username.
cat /etc/*-release    # Find out what distribution of Linux you're running

# Timestamp things:
date +%F-%T	# gives 2019-07-17-20:10:16

# Less important but still really cool commands!
say [-v] [name] "<phrase>"		# 
write	 <user>					# start a chat with <user> on your server
    - You are immediately put into "write mode". Now you can send messages back and forth.
    - Press 'Ctrl+C' or 'Esc' to exit write mode.
mesg [y|n]					# allow [y] people to send you messages using 'write' or not [n]
sleep 7  # make the shell sleep for 7 seconds
```

## Advanced Commands

### awk

An extremely powerful file-processing language.

```bash
awk 'BEGIN{begincmds} {cmd_applied_to_each_line} END{endcmds}' <file>

# Sum the second column and specify the column delimiter to be a comma:
awk -F','  '{sum+=$2} END{print sum}' file.txt

# Use `awk` like a calculator:
awk "BEGIN {print 100/3}"  # 33.3333
awk "BEGIN {x=100/3; y=6; z=x*y; print z}"  # 200
awk "BEGIN {printf \"%.2f\n\", 100/3}"  # 33.33
```

### sed (stream editor)

A powerful, ubiquitous command. Learn it well.

```bash
# replace every instance of "MASS" in yourfile.txt with `20`.
zdmass=20
sed -i "s/MASS/${zdmass}/g" yourfile.txt

# Print the 4th line from a file:
sed "4q;d" <file>
# Explanation:
# q = quits once line is reached
# d usually deletes the last line, except when program quits.

# Strip python/bash comments from a file:
sed -i -e 's/#.*$//g' -e '/^$/d' <file>
# the '-e' executes another instance of sed, like piping.
# '-i' is "in place" so it modifies <file>

# sed, in place, on a Mac:
sed -i '' -e "s|STORAGESITE|${storageSiteGEN}|g" DELETEFILE.txt 

echo "1e-2" | sed "s#^+*[^e]#&.000000#;s#.*e-#&0#"  # makes 1e-2 become 1.000000e-02
# can also do:
sed "s#^[0-9]*[^e]#&.000000#;s#.*e-#&0#"
```

### Finding files

```bash
find
find ./ -name "*plots*"								# find all files with name plots in this dir and subsequent dir
find /<path> -mtime +180 -size +1G					# find files with mod times >180 days and size>1GB
find . -type d -exec touch '{}/__init__.py' \;				# create (touch) a __init__.py file in every dir and subsequent dir
find . -type f -printf '%s\t%p\n' | sort -nr | head -n 30		# find the 30 biggest files in your working area, sorted 
find . -type f -printf '%T@ %p\n' | sort -n | tail -20 | cut -f2- -d" "		# find the 20 most recently modified files
find . -name "*.css" -exec sed -i -r 's/MASS/mass/g' {} \;	# use sed on every found file (the {} indicates a found file)
find ~/src/ -newer main.css							# find files newer than main.css

locate
locate -i <file_to_be_found>		# searches computer's database. -i flag means case insensitive
```

## Wildcards

**Wild**ly important! [Here's a tutorial.](https://linuxhint.com/bash_wildcard_tutorial/)

## Some Bash Magic

```bash
!        # This is the 'bang' operator, an iconic part of bash.
!$       # The argument of the last command. Try: `cd !$`
!!       # Execute the last command from history.
!cp      # Run the last `cp` command from your history. VERY useful.
!cat:p   # Print the last `cp` command you used to stdout, add that command to history, do not execute.
^ls^rm   # Replace `ls` in the last command with `rm`
$?       # The return value from last cmd (0=success).
history  # Check your history; displays command numbers.
!<cmd_num>  # execute command number <cmd_num>
<space>cmd  # will not add 'cmd' to history!
```

Redirect the output and errors to log.txt

```bash
cmd 2>&1 log.txt
# Redirect the output and errors to both log.txt and the screen simultaneously:
cmd 2>&1 | tee log.txt
```

Batch expansion

```bash
cp /etc/rc.conf{,-old}  # will make copy of 'rc.conf' called 'rc.conf-old'
mkdir newdir{1,2,3}     # will make newdir1, newdir2, newdir3
# It's as if the filepath "gets distributed" over the braces
# This is a good way to mv files and make backups
```

Difference between 'source' and 'export':

```bash
source <script.sh>  # effectively the same as: . <script.sh>; executes script in current shell
export VAR=value    # saves value as a new environmental VAR available to child processes
```

[Command line language translator.](https://www.ostechnix.com/use-google-translate-commandline-linux/)

### grep (global regular expression print)

Print lines in files that contain some `phrase`.
A command to add to your daily Linux usage!

```bash
grep   <string>   <files_to_be_searched>

grep -E -r "*<word>*" ./* 	# search the contents of every file for *<word>*, recursively starting from ./*
# equivalent to doing: egrep
```

## GNU screen! (a terminal multiplexer)**

- Start a persistent remote terminal
- That way, you won't lose your work if you get disconnected
- After making a new screen, you should do: `source ~/.bash_profile` to get your normal settings

```bash
screen -S <screen_name>	# start a bash environment session ("screen")
ctrl+a, then Esc			# enters "copy/scrollback mode", which lets you scroll! 
- navigate copy mode using Vim commands!
- Hit `Enter` to highlight text. Hit `Enter` again to copy it. Paste with Ctrl+a, then `]`
- Hit `q` or `Esc` to exit copy mode.
ctrl+a, then d				# detach from the session (remember though that it's still active!)
screen -ls				# see what sessions are active
screen -r <name>			# reattach to active session (instead of <name> can also use: <screen_pid>)
exit						# terminate session
kill <screen_pid>			# kill frozen screen session
ctrl+a then s				# split screens horizontally
ctrl+a then v				# split screens vertically
ctrl+a then Tab			# switch between split screen regions
ctrl+a then c				# begins a virtual window in a blank screen session
ctrl+a then "				# see list of all active windows inside session
```

### tmux (terminal multiplexer)

Nice for splitting the terminal into multiple screens.
All commands start with: `Ctrl+b`
Tmux reads from your config file: `~/.tmux.conf`

Shell commands:

```bash
tmux							# Start a new session.
tmux kill-session -t <name>		# Kill session <name>.
tmux new -s <name>				# Name the tmux session.
tmux ls							# List all running sessions.
tmux attach-session -t <name>	# Reattach to session <name>.
# Inside tmux commands:
Ctrl+b, ?  # Show all commands.
Ctrl+b, d  # Detach from session.
Ctrl+b, c  # Create a new window, with a shell.
Ctrl+b, w  # Choose window from a list.
Ctrl+b, 2  # Switch to window number 2.
Ctrl+b, ,  # Rename the current window. 
Ctrl+b, %  # Split current pane vertically.
Ctrl+b, <doublequote>  # Split current pane horizontally.
Ctrl+b, o  # Go to next pane.
Ctrl+b, ;  # Toggle between panes.
Ctrl+b, {  # Move current pane left.
Ctrl+b, }  # Move current pane right.
Ctrl+b, x  # Close current pane.
exit       # Permanently close the session.
```

### wget

Download stuff.

```bash
wget <url>	# download whatever url from the web
wget -r --no-parent -A.pdf http://tier2.ihepa.ufl.edu/~rosedj1/DarkZ/MG5vsJHUGen_bestkinematics_GENlevel_WITHfidcuts/
# Downloads recursively, without looking at parent directories, and globbing all .pdf
```

### tar

Zip and unzip files.

```bash
tar -cf <tarball> foo bar				# create <tarball> using files foo and bar
tar -xf <tarball>					# unzip all of <tarball>
tar -xvf <tarball> <file1> <file2>		# untar specific files from tarball
# x=extract, v=verbose, f=file, 
```

### Memory usage

```bash
du 				# "disk usage"; good for find which files or dirs are taking up the most space
du -h <dir>		# print size of <dir> in human-readable format 
du -sh ./			# sums up the total of current workspace and all subdirs

df -h				# "disk filesystem", shows usage of memory on entire filesystem 
```

Copy multiple files from remote server to local:

```bash
scp <username>@<host>:/path/to/files/\{file1, file2, file3\} .
```

Mount a remote disk on your local system:

```bash
sshfs <UN@remote_host> <local/dir>
# Unmount when finished:
umount <local/dir>
```

## Read up on these commands

```bash
rcp
set
set -e 			# exit on first error?
set -u 			# catch unset variables?
set -o  # Check your bash settings.
rsync <file> <destination>
#rsync -av <file> <destination>

ps aux | grep <task>		# see active processes

# Study this code below and see if syntax is useful:
# if rnum allows, multiply by 10 to avoid multiple runs
# with the same seed across the workflow
run_random_start=$(($rnum*10))
# otherwise don't change the seed and increase number of events as 10000 if n_evt<50000 or n_evt/9 otherwise
if [  $run_random_start -gt "89999990" ]; then
    run_random_start=$rnum
    max_events_per_iteration=$(( $nevt > 10000*9 ? ($nevt / 9) + ($nevt % 9 > 0) : 10000 ))
fi

# Learn more about nohup:
nohup ./gridpack_generation_patched06032014.sh tt 1nd > tt.log &

tr "." "_"			# translates all "." chars into "_" (used with piping)
perl -ne 'print if /pattern1/ xor /pattern2/'

What does this do?
model=HAHM_variablesw_v3_UFO.tar.gz
if [[ $model = *[!\ ]* ]]; then...
```

Use a specific interpreter to execute a file:
`#!/usr/bin/env python`

- Find out where your command lives: `type -a <cmd>`

Environment variables can be modified using 'export':

```bash
export VARIABLE=value
export PYTHONPATH=${PYTHONPATH}:</path/to/modules>	# appending ':</path/to/modules>' to PYTHONPATH env var

# Interesting env vars:
SHELL			# hopefully bash
HOSTNAME		# host
SCRAM_ARCH		# cmssw architecture
USER			# You!
PWD				# current working dir
PS1				# bash prompt
LS_COLORS		# colors you see when you do 'ls'
MAIL
EDITOR
```

Customize your prompt (you can even add a command to be executed INSIDE the prompt):

```bash
PS1="[\d \t] `uptime` \u@\h\n\w\$ "

# Prompt settings:
# A bell character: \a
# The date, in “Weekday Month Date” format (e.g., “Tue May 26”): \d
# The format is passed to strftime(3) and the result is inserted into the prompt string; an empty format results in a locale-specific time representation. The braces are required: \D{format}
# An escape character: \e
# The hostname, up to the first ‘.’: \h
# The hostname: \H
# The number of jobs currently managed by the shell: \j
# The basename of the shell’s terminal device name: \l
# A newline: \n
# A carriage return: \r
# The name of the shell, the basename of $0 (the portion following the final slash): \s
# The time, in 24-hour HH:MM:SS format: \t
# The time, in 12-hour HH:MM:SS format: \T
# The time, in 12-hour am/pm format: \@
# The time, in 24-hour HH:MM format: \A
# The username of the current user: \u
# The version of Bash (e.g., 2.00): \v
# The release of Bash, version + patchlevel (e.g., 2.00.0): \V
# The current working directory, with $HOME abbreviated with a tilde (uses the $PROMPT_DIRTRIM variable): \w
# The basename of $PWD, with $HOME abbreviated with a tilde: \W
# The history number of this command: \!
# The command number of this command: \#
# If the effective uid is 0, #, otherwise $: \$
# The character whose ASCII code is the octal value nnn: \nnn
# A backslash: \\
# Begin a sequence of non-printing characters. This could be used to embed a terminal control sequence into the prompt: \[
# End a sequence of non-printing characters: \]
# FINISH GETTING THE REST OF THESE PROMPT SETTINGS! 
# e.g. colors
```

Open plots while ssh'ed:

```bash
display 
eog <png>		# quickly open png files
```

## Cool Bash Tricks

Quickly rename a bunch of files in a dir:

```bash
for file in *.pdf;
    do mv "$file" "${file/.pdf/.txt}";  # ${file/replace_this/with_this}
done
```

Make a bunch of dir's quickly:

```bash
mkdir newdir{1..20}  # makes newdir1, newdir2, ..., newdir20
```

Iterate over floats:

```bash
for k in $(seq 0 0.2 1);  # seq <start> <interval> <stop>
    do
    echo "$k"
done
# seq has all kinds of flags for formatting!
```

Check if a dir exists. If it doesn't, then make it:

```bash
[ -d possibledir ] || mkdir possibledir
# The LHS checks if the directory is there.
# If it is, bash returns 1, and the OR statement ('||') is satisfied.
# Otherwise, mkdir
```

Timestamp things:

```bash
function timestampit { date +"%y%m%d_%H%M%S"; }
```

Terminal Shortcuts:

```bash
Ctrl-A			# quickly go to BEGINNING of line in terminal
Ctrl-E			# quickly go to END of line in terminal
Ctrl-W			# delete whole WORD behind cursor
Ctrl-U			# delete whole LINE BEHIND cursor
Ctrl-K			# delete whole LINE AFTER cursorCtrl-R, then <cmd>	# reverse-search your command history for <cmd>
Option-Left		# move quickly to the next word to the left
Cmd-Right		# switch between terminal WINDOWS
Cmd-Shift-Right	# switch between terminal TABS within window
```

`time ./<script.sh>`  # time how long a script takes to run

- this will send three times to stdout:
  - real (actual run time)
  - user
  - sys

Background Jobs:

```bash
<cmd> &			# runs <cmd> in a background subshell
fg				# bring a background process to foreground
jobs				# see list of all background processes
Ctrl+Z			# pause current job and return to shell
Ctrl+S			# pause a job, but DON'T return to shell
Ctrl+Q			# resume paused job in foreground
bg				# resume current job in background
(sleep 3 && echo 'I just woke up') >/tmp/output.txt &		# group commands and redirect stdout!
# here the '&&' means to do the second command ONLY IF the first command was successful
```

.bash_profile is executed for login shells, while .bashrc is executed for interactive non-login shells.

Execute shell script in current shell, instead of forking into a subshell:
`. ./<script.sh>` (dot space dot forward-slash).
- N.B. this is nearly the same as doing: `source <script.sh>`

Check to see if some command succeeded (`return 0`):

```bash
<some_command>
if [ $? -eq 0 ]; then
    echo OK
else
    echo FAIL
fi
```

"Divide out" strings:

```bash
MG="MG5_aMC_v2.6.0.tar.gz"
MG_EXT=".tar.gz"
echo ${MG%$MG_EXT}
# Prints: MG5_aMC_v2.6.0
```

- [AFS Permissions](https://computing.cs.cmu.edu/help-support/afs-acls)

## Resources

- Here's the [friendly Linux Tutorial](https://ryanstutorials.net/linuxtutorial/commandline.php)
that I used to learn Linux.
- Tutorials from [Linux.com](https://www.linux.com/training-tutorials/).


**MOVE THESE NOTES TO PARALLEL PROCESSING**
computer cluster:
folders aren’t contained on just one computer, 
but network mounts can make it look like they are
server uses a "load balancer"
puts each user on a variety of nodes to balance the load of resource usage

- **Absolute** file paths ("root paths") being with `/`.
- **Relative** files paths use: `./`
