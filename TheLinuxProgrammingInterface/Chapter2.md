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

#### File I/O Model

One of the distinguishing features of the I/O model on UNIX systems is the concept of universality of I/O. This means that the same system calls (open(), read(), write(), close(), and so on) are used to perform I/O on all types of files, including devices. (The kernel translates the application’s I/O requests into appropriate file-system or device-driver operations that perform I/O on the target file or device.)

##### File descriptors

The I/O system calls refer to open files using a file descriptor, a (usually small) nonnegative integer. A file descriptor is typically obtained by a call to open(), which takes a pathname argument specifying a file upon which I/O is to be performed.

##### The stdio library

To perform file I/O, C programs typically employ I/O functions contained in the standard C library. This set of functions, referred to as the stdio library, includes fopen(), fclose(), scanf(), printf(), fgets(), fputs(), and so on. The stdio functions are layered on top of the I/O system calls (open(), close(), read(), write(), and so on).

#### Programs

Programs normally exist in two forms. The first form is source code, human-readable text consisting of a series of statements written in a programming language such as C. To be executed, source code must be converted to the second form: binary machine-language instructions that the computer can understand. (This contrasts with a script, which is a text file containing commands to be directly processed by a program such as a shell or other command interpreter.) The two meanings of the term program are normally considered synonymous, since the step of compiling and linking converts source code into semantically equivalent binary machine code.

##### Filters

A filter is the name often applied to a program that reads its input from stdin, performs some transformation of that input, and writes the transformed data to stdout.

##### Command-line arguments

In C, programs can access the command-line arguments, the words that are supplied on the command line when the program is run.

#### Processes

Put most simply, a process is an instance of an executing program. When a program is executed, the kernel loads the code of the program into virtual memory, allocates space for program variables, and sets up kernel bookkeeping data structures to record various information (such as process ID, termination status, user IDs, and group IDs) about the process.

From a kernel point of view, processes are the entities among which the kernel must share the various resources of the computer. For resources that are limited, such as memory, the kernel initially allocates some amount of the resource to the process, and adjusts this allocation over the lifetime of the process in response to the demands of the process and the overall system demand for that resource.

##### Process memory layout
A process is logically divided into the following parts, known as segments:

* Text: the instructions of the program.
* Data: the static variables used by the program.
* Heap: an area from which programs can dynamically allocate extra memory.
* Stack: a piece of memory that grows and shrinks as functions are called and return and that is used to allocate storage for local variables and function call linkage information.

##### Process creation and program execution

A process can create a new process using the fork() system call. The process that calls fork() is referred to as the parent process, and the new process is referred to as the child process. The kernel creates the child process by making a duplicate of the parent process. The child inherits copies of the parent’s data, stack, and heap segments, which it may then modify independently of the parent’s copies. (The program text, which is placed in memory marked as read-only, is shared by the two processes.)

##### Process ID and parent process ID

Each process has a unique integer process identifier (PID). Each process also has a parent process identifier (PPID) attribute, which identifies the process that requested the kernel to create this process.

##### Process termination and termination status

A process can terminate in one of two ways: by requesting its own termination using the _exit() system call (or the related exit() library function), or by being killed by the delivery of a signal.

##### Process user and group identifiers (credentials)
Each process has a number of associated user IDs (UIDs) and group IDs (GIDs).These include:

* Real user ID and real group ID
* Effective user ID and effective group ID
* Supplementary group IDs

##### Privileged processes

Traditionally, on UNIX systems, a privileged process is one whose effective user ID is 0 (superuser). Such a process bypasses the permission restrictions normally applied by the kernel. By contrast, the term unprivileged (or nonprivileged) is applied to processes run by other users. Such processes have a nonzero effective user ID and must abide by the permission rules enforced by the kernel.

##### Capabilities

Since kernel 2.2, Linux divides the privileges traditionally accorded to the superuser into a set of distinct units called capabilities. Each privileged operation is associated with a particular capability, and a process can perform an operation only if it has the corresponding capability. A traditional superuser process (effective user ID of 0) corresponds to a process with all capabilities enabled.

##### The init process

When booting the system, the kernel creates a special process called init, the “parent of all processes,” which is derived from the program file /sbin/init . All processes on the system are created (using fork()) either by init or by one of its descendants. The init process always has the process ID 1 and runs with superuser privileges. The init process can’t be killed (not even by the superuser), and it terminates only when the system is shut down. The main task of init is to create and monitor a range of processes required by a running system.

##### Daemon processes

A daemon is a special-purpose process that is created and handled by the system in the same way as other processes, but which is distinguished by the following characteristics:
* It is long-lived. A daemon process is often started at system boot and remains in existence until the system is shut down.
* It runs in the background, and has no controlling terminal from which it can read input or to which it can write output.

##### Environment list

Each process has an environment list, which is a set of environment variables that are maintained within the user-space memory of the process. Each element of this list consists of a name and an associated value. When a new process is created via fork(), it inherits a copy of its parent’s environment. Thus, the environment provides a mechanism for a parent process to communicate information to a child process. When a process replaces the program that it is running using exec(), the new program either inherits the environment used by the old program or receives a new environment specified as part of the exec() call.

##### Resource limits

Each process consumes resources, such as open files, memory, and CPU time. Using the setrlimit() system call, a process can establish upper limits on its consumption of various resources. Each such resource limit has two associated values: a soft limit, which limits the amount of the resource that the process may consume; and a hard limit, which is a ceiling on the value to which the soft limit may be adjusted. An unprivileged process may change its soft limit for a particular resource to any value in the range from zero up to the corresponding hard limit, but can only lower its hard limit.