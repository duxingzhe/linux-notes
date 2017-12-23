### Fundamental Concepts

#### The Core Operating System: The Kernel

The term operating system is commonly used with two different meanings:

* To denote the entire package consisting of the central software managing a computer’s resources and all of the accompanying standard software tools, such as command-line interpreters, graphical user interfaces, file utilities, and editors.
* More narrowly, to refer to the central software that manages and allocates computer resources (i.e., the CPU, RAM, and devices).

##### Tasks performed by the kernel

the kernel performs the following tasks:

* Process scheduling
* Memory management
* Provision of a file system
* Creation and ternimation of processes
* Acess to devices
* Networking
* Provision of a system call application programming interface

##### Kernel mode and user mode

Modern processor architectures typically allow the CPU to operate in at least two different modes: user mode and kernel mode (sometimes also referred to as supervisor mode). Hardware instructions allow switching from one mode to the other. Correspondingly, areas of virtual memory can be marked as being part of user space or kernel space.

##### Process versus kernel views of the system

In many everyday programming tasks, we are accustomed to thinking about programming in a process-oriented way.

By contrast, a running system has one kernel that knows and controls everything. The kernel facilitates the running of all processes on the system. The kernel decides which process will next obtain access to the CPU, when it will do so, and for how long. The kernel maintains data structures containing information about all running processes and updates these structures as processes are created, change state, and terminate. The kernel maintains all of the low-level data structures that enable the filenames used by programs to be translated into physical locations on the disk. The kernel also maintains data structures that map the virtual memory of each process into the physical memory of the computer and the swap area(s) on disk. All communication between processes is done via mechanisms provided by the kernel. In response to requests from processes, the kernel creates new processes and terminates existing processes. Lastly, the kernel (in particular, device drivers) performs all direct communication with input and output devices, transferring information to and from user processes as required.

#### The Shell

A shell is a special-purpose program designed to read commands typed by a user and execute appropriate programs in response to those commands. Such a program is sometimes known as a command interpreter.

Whereas on some operating systems the command interpreter is an integral part of the kernel, on UNIX systems, the shell is a user process.

A number of important shells have appeared over time:

* Bourne shell (sh): This is the oldest of the widely used shells, and was written by Steve Bourne. It was the standard shell for Seventh Edition UNIX. The Bourne shell contains many of the features familiar in all shells: I/O redirection, pipelines, filename generation (globbing), variables, manipulation of environment variables, command substitution, background command execution, and functions. All later UNIX implementations include the Bourne shell in addition to any other shells they might provide.
* C shell (csh): This shell was written by Bill Joy at the University of California at Berkeley. The name derives from the resemblance of many of the flow-control constructs of this shell to those of the C  programming language. The C shell provided several useful interactive features unavailable in the Bourne shell, including command history, command-line editing, job control, and aliases. The C shell was not backward compatible with the Bourne shell. Although the standard interactive shell on BSD was the C shell, shell scripts (described in a moment) were usually written for the Bourne shell, so as to be portable across
all UNIX implementations.
* Korn shell (ksh): This shell was written as the successor to the Bourne shell by David Korn at AT&T Bell Laboratories. While maintaining backward compatibility with the Bourne shell, it also incorporated interactive features similar to those provided by the C shell.
* Bourne again shell (bash): This shell is the GNU project’s reimplementation of the Bourne shell. It supplies interactive features similar to those availablein the C and Korn shells. The principal authors of bash are Brian Fox and Chet Ramey. Bash is probably the most widely used shell on Linux. (On Linux, the Bourne shell, sh, is actually provided by bash emulating sh as closely as possible.)

>POSIX.2-1992 specified a standard for the shell that was based on the then current version of the Korn shell. Nowadays, the Korn shell and bash both conform to POSIX, but provide a number of extensions to the standard, and many of these extensions differ between the two shells.

#### Users and Groups

Each user on the system is uniquely identified, and users may belong to groups.

##### Users
Every user of the system has a unique login name (username) and a corresponding numeric user ID (UID). For each user, these are defined by a line in the system password file, /etc/passwd , which includes the following additional information:

* Group ID: the numeric group ID of the first of the groups of which the user is a member.
* Home directory: the initial directory into which the user is placed after logging in.
* Login shell: the name of the program to be executed to interpret user commands.

The password record may also include the user’s password, in encrypted form. However, for security reasons, the password is often stored in the separate shadow password file, which is readable only by privileged users.

##### Groups

For administrative purposes—in particular, for controlling access to files and other system resources—it is useful to organize users into groups.

Each group is identified by a single line in the system group file, /etc/group , which includes the following information:

* Group name: the (unique) name of the group.
* Group ID (GID): the numeric ID associated with this group.
* User list: a comma-separated list of login names of users who are members of this group (and who are not otherwise identified as members of the group by virtue of the group ID field of their password file record).

##### Superuser

One user, known as the superuser, has special privileges within the system. The superuser account has user ID 0, and normally has the login name root. On typical UNIX systems, the superuser bypasses all permission checks in the system.