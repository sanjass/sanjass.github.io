---
layout: post
comments: true
title: Three tips for more efficient project space navigation
---

If you ever worked on a project hosted on a remote server you might have
 found yourself repeatedly doing these things:
 
 - write an ssh command to connect to your server
 - write a long sshfs command to mount your remote project directory on your local filesystem
 - navigate to your project directory on the remote server and activate any virtual environments
 - set up any local variables such as exporting a python path
 - start up any services to run in the background
 - run a script that takes a long time to finish (maybe training a network etc)

When you first start on your project typing these commands might not seem like a big deal since you have bigger things to
worry about. But after a while, you get tired going through this boring routine just to be able to actually
start working on your project.


#### Is there a better way?

Sure there is! These are **3 tips** I want to share with you about how I optimize the time and effort spent
on these boring commands that one just has to go through. Note that this is just my routine for my types
of projects (mostly Python and Linux based), and it definitely doesn't mean it is the best or appropriate for your use case. 


*This post belongs to the imaginary series about "Things I wish I had known when I was x", and while it might be basic for
seasoned programmers, I hope it will be useful for the ones who are just starting.*
### Tip #1 Use command-line aliases

As the name suggests, command-line aliases are just unique names you can attach to a custom bash command (usually a longer one that you need to execute often).
This saves you time and effort because you don't have to type the long command but only the name of the alias.
To see all your existing aliases just type `alias` on the command-line. A sample output is this:
 ```bash
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'
 ```
Based on this output we know that if we write `la` on the command-line it would be equivalent to writing `ls -A` (lists all files, including hidden ones as well).
Now, sure this is convenient, but it is not too painful to just write `ls -A`, right? True, but how about this command:
```bash
sshfs <user_name>@<server_ip>:<path_to_remote_directory> <path_to_local_directory> -o <options>; ssh <user_name>@<remote-ip>
```
This command mounts a remote directory to a local one and then it ssh-es to the remote server. This might be a common pattern for
developers whose projects are hosted on remote servers.
Manually typing this command for specific username/ip values should happen only once in your life. At the very least, in future sittings
you should have the command handy to copy-paste it. Or even better, create an alias for it!

To create an alias for this long command add the following line, with the appropriate edits, on a new line in your `~/.bashrc` file (best to do it after the last of the current aliases):
```bash
alias myaliasname="sshfs <user_name>@<server_ip>:<path_to_remote_directory> <path_to_local_directory> -o <options>; ssh <user_name>@<remote-ip>"
```
Now just restart your shell (open new window or do `source ~/.bashrc`) and the next time you type `myaliasname` it should perform the magic for you (you might
still need to type them passwords though).

To read more about aliases, see [here](https://www.tecmint.com/create-alias-in-linux/)

### Tip #2 Create a start.sh file in your project root
Some common things you do when you navigate to your project directory for the first time in your sitting are things
like activating a virtual environment and setting the root path. And of course, there is some typing involved and you need to know
the name of your virtual environment for the particular project and you need to remember the syntax for exporting the path.
If you work on several projects at a time or just have a bad memory, this can become burdensome. Why not
just type the commands once, put them in bash file in your root directory and simply execute that file each time you begin working?

- create a `start.sh` file in the root of your project: `touch start.sh`
- open it, type the commands you want executed at the start and save
- to execute the file run `source start.sh` (or equivalently `. start.sh`)

An example `start.sh` file might look as simple as this:
```bash
conda activate myenvname; 
export PYTHONPATH=`pwd`;
```

If you're wondering why we didn't just create an alias for this, you're on the right track! We could have
created an alias, but note two things:

- aliases are accessible only on the machine on which they were created (and only for the user that created them if the alias is in `~/.bashrc`), so
your project partners who might be working on a local copy (via `git`) will not have access to this alias, whereas the `start.sh` file will be
accessible to everyone, assuming it is tracked by the version control system
- some commands in the `start.sh` file traditionally assume the current directory is the project's root directory
such as commands which contain the `pwd` (print working directory) as a subcommand. 

Now, instead of typing long setup commands each time you sit to do your work, you can just navigate to your project
directory and type `. start.sh` and everything will be done automatically for you!

### Tip #3 Use tmux to have access to multiple terminals
If you ever needed to run a script on a remote server that takes hours or even days to execute (maybe training or processing huge dataset)
you've surely asked yourself if you really need to be logged in and keeping the terminal window open the whole time. Terminal multiplexers come to the
rescue! One such tool is `tmux`, another one is `screen`. I prefer `tmux` and it seems to be more popular so you might as well use that.
To read more about terminal multiplexers and tmux in particular, you can look at [this page](https://linuxconfig.org/an-introduction-to-terminal-multiplexers), or you
can simply search for online tutorials.

Some use-cases for tmux:

- Run multiple python scripts in different windows, while retaining access to the main one that allows me to do other work
- Run time-consuming tasks without losing access to terminal
- Run tensorboard or streamlit uninterruptedly in the background

Note that when you restart or turn-off the remote server all tmux sessions will be lost. Assuming your server
runs continuously (unless something goes wrong), your sessions will be accessible even after you logout.

## Conclusion 
In this post I gave 3 tips and tricks I use to increase my efficiency before getting some real work done.
Knowing that I can access my server easily via the alias, that I can setup my environment easily via the start.sh file and I can run
long scripts in the background definitely makes working on projects less daunting and before all it makes me
feel like more of a geek (anyone who has typed several commands fast in the terminal knows what I'm talking about).
This list is short and there are definitely things I haven't told you (yet!) but I am open to suggestions about how can
we extend this list with useful things that school doesn't teach you.