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

#### Single Directory Hierarchy, Directories, Links, and Files

At the base of this hierarchy is the root directory, named / (slash). All files and directories are children or further removed descendants of the root directory. Figure 2-1 shows an example of this hierarchical file structure.

##### File types

Within the file system, each file is marked with a type, indicating what kind of file it is. One of these file types denotes ordinary data files, which are usually called regular or plain files to distinguish them from other file types. These other file types include devices, pipes, sockets, directories, and symbolic links.

##### Directories and links

A directory is a special file whose contents take the form of a table of filenames coupled with references to the corresponding files.

Every directory contains at least two entries: . (dot), which is a link to the directory itself, and .. (dot-dot), which is a link to its parent directory, the directory above it in the hierarchy. Every directory, except the root directory, has a parent. For the root directory, the dot-dot entry is a link to the root directory itself (thus, /.. equates to / ).

##### Symbolic links

Like a normal link, a symbolic link provides an alternative name for a file. But whereas a normal link is a filename-plus-pointer entry in a directory list, a symbolic link is a specially marked file containing the name of another file. (In other words, a symbolic link has a filename-plus-pointer entry in a directory, and the file referred to by the pointer contains a string that names another file.)

##### Filenames

On most Linux file systems, filenames can be up to 255 characters long. Filenames may contain any characters except slashes ( / ) and null characters ( \0 ). However, it is advisable to employ only letters and digits, and the . (period), _ (underscore), and - (hyphen) characters. This 65-character set, [-._a-zA-Z0-9] , is referred to in SUSv3 as the portable filename character set.

##### Pathnames

A pathname is a string consisting of an optional initial slash (/) followed by a series of filenames separated by slashes. All but the last of these component filenames identifies a directory (or a symbolic link that resolves to a directory). The last component of a pathname may identify any type of file, including a directory.

A pathname describes the location of a file within the single directory hier-
archy, and is either absolute or relative:

* An absolute pathname begins with a slash (/) and specifies the location of a file with respect to the root directory. Examples of absolute pathnames for files in Figure 2-1 are /home/mtk/.bashrc , /usr/include , and / (the pathname of the root directory).
* A relative pathname specifies the location of a file relative to a process’s current working directory (see below), and is distinguished from an absolute pathname by the absence of an initial slash. In Figure 2-1, from the directory usr, the file types.h could be referenced using the relative pathname include/sys/types.h, while from the directory avr , the file .bashrc could be accessed using the relative pathname ../mtk/.bashrc.

##### Current working directory

Each process has a current working directory (sometimes just referred to as the process’s working directory or current directory). This is the process’s “current location” within the single directory hierarchy, and it is from this directory that relative pathnames are interpreted for the process.

##### File ownership and permissions

Each file has an associated user ID and group ID that define the owner of the file and the group to which it belongs. The ownership of a file is used to determine the access rights available to users of the file.