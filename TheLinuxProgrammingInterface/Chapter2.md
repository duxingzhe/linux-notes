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

#### Memory Mappings

The mmap() system call creates a new memory mapping in the calling process’s virtual address space.

Mappings fall into two categories:

* A file mapping maps a region of a file into the calling process’s virtual memory. Once mapped, the file’s contents can be accessed by operations on the bytes in the corresponding memory region. The pages of the mapping are automatically loaded from the file as required.
* By contrast, an anonymous mapping doesn’t have a corresponding file. Instead, the pages of the mapping are initialized to 0.

#### Static and Shared Libraries

An object library is a file containing the compiled object code for a (usually logically related) set of functions that may be called from application programs.

Modern UNIX systems provide two types of object libraries: static libraries and shared libraries.

##### Static libraries

Static libraries (sometimes also known as archives) were the only type of library on early UNIX systems. A static library is essentially a structured bundle of compiled object modules. To use functions from a static library, we specify that library in the link command used to build a program. After resolving the various function references from the main program to the modules in the static library, the linker extracts copies of the required object modules from the library and copies these into the resulting executable file. We say that such a program is statically linked.

##### Shared libraries

Shared libraries were designed to address the problems with static libraries.

If a program is linked against a shared library, then, instead of copying object modules from the library into the executable, the linker just writes a record into the executable to indicate that at run time the executable needs to use that shared library. When the executable is loaded into memory at run time, a program called the dynamic linker ensures that the shared libraries required by the executable are found and loaded into memory, and performs run-time linking to resolve the function calls in the executable to the corresponding definitions in the shared libraries. At run time, only a single copy of the code of the shared library needs to be resident in memory; all running programs can use that copy.

#### Interprocess Communication and Synchronization

One way for processes to communicate is by reading and writing information in disk files. However, for many applications, this is too slow and inflexible. Therefore, Linux, like all modern UNIX implementations, provides a rich set of mech-
anisms for interprocess communication (IPC), including the following:
* signals, which are used to indicate that an event has occurred;
pipes (familiar to shell users as the | operator) and FIFOs, which can be used to transfer data between processes;
* sockets, which can be used to transfer data from one process to another, either on the same host computer or on different hosts connected by a network;
* file locking, which allows a process to lock regions of a file in order to prevent other processes from reading or updating the file contents;
* message queues, which are used to exchange messages (packets of data) between processes;
* semaphores, which are used to synchronize the actions of processes; and shared memory, which allows two or more processes to share a piece of memory. When one process changes the contents of the shared memory, all of the other processes can immediately see the changes.

The wide variety of IPC mechanisms on UNIX systems, with sometimes overlapping functionality, is in part due to their evolution under different variants of the UNIX system and the requirements of various standards.

#### Signals

Signals are often described as “software interrupts.” The arrival of a signal informs a process that some event or exceptional condition has occurred. There are various types of signals, each of which identifies a different event or condition. Each signal type is identified by a different integer, defined with symbolic names of the form SIGxxxx.

Signals are sent to a process by the kernel, by another process (with suitable permissions), or by the process itself. For example, the kernel may send a signal to a process when one of the following occurs:

* the user typed the interrupt character (usually Control-C) on the keyboard;
* one of the process’s children has terminated;
* a timer (alarm clock) set by the process has expired; or
* the process attempted to access an invalid memory address.

Within the shell, the kill command can be used to send a signal to a process. The kill() system call provides the same facility within programs.

When a process receives a signal, it takes one of the following actions, depending on the signal:

* it ignores the signal;
* it is killed by the signal; or
* it is suspended until later being resumed by receipt of a special-purpose signal.

For most signal types, instead of accepting the default signal action, a program can choose to ignore the signal (useful if the default action for the signal is something other than being ignored), or to establish a signal handler. A signal handler is a programmer-defined function that is automatically invoked when the signal is delivered to the process. This function performs some action appropriate to the condition that generated the signal.

A signal handler is a programmer-defined function that is automatically invoked when the signal is delivered to the process.

#### Threads

In modern UNIX implementations, each process can have multiple threads of execution. One way of envisaging threads is as a set of processes that share the same virtual memory, as well as a range of other attributes. Each thread is executing the same program code and shares the same data area and heap.

The threading API provides condition variables and mutexes, which are primitives that enable the threads of a process to communicate and synchronize their actions, in particular, their use of shared variables. Threads can also communicate with one another using the IPC and synchronization mechanisms described in Section 2.10.

#### Process Groups and Shell Job Control

Each program executed by the shell is started in a new process.

All major shells, except the Bourne shell, provide an interactive feature called job control, which allows the user to simultaneously execute and manipulate multiple commands or pipelines. In job-control shells, all of the processes in a pipeline are placed in a new process group or job. (In the simple case of a shell command line containing a single command, a new process group containing just a single process is created.) Each process in a process group has the same integer process group identifier, which is the same as the process ID of one of the processes in the group, termed the process group leader.

#### Sessions, Controlling Terminals, and Controlling Processes

A session is a collection of process groups ( jobs). All of the processes in a session have the same session identifier. A session leader is the process that created the session, and its process ID becomes the session ID.

As a consequence of opening the controlling terminal, the session leader becomes the controlling process for the terminal. The controlling process receives a SIGHUP signal if a terminal disconnect occurs (e.g., if the terminal window is closed).
At any point in time, one process group in a session is the foreground process group ( foreground job), which may read input from the terminal and send output to it. If the user types the interrupt character (usually Control-C) or the suspend character (usually Control-Z) on the controlling terminal, then the terminal driver sends a signal that kills or suspends (i.e., stops) the foreground process group. A session can have any number of background process groups (background jobs), which are created by terminating a command with the ampersand (&) character.

#### Pseudoterminals

A pseudoterminal is a pair of connected virtual devices, known as the master and slave. This device pair provides an IPC channel allowing data to be transferred in both directions between the two devices.

Date and Time

Two types of time are of interest to a process:

* Real time is measured either from some standard point (calendar time) or from some fixed point, typically the start, in the life of a process (elapsed or wall clock time). On UNIX systems, calendar time is measured in seconds since midnight on the morning of January 1, 1970, Universal Coordinated Time (usually abbreviated UTC), and coordinated on the base point for timezones defined by the longitudinal line passing through Greenwich, England. This date, which is close to the birth of the UNIX system, is referred to as the Epoch.

* Process time, also called CPU time, is the total amount of CPU time that a process
has used since starting. CPU time is further divided into system CPU time, the
time spent executing code in kernel mode (i.e., executing system calls and per-
forming other kernel services on behalf of the process), and user CPU time, the
time spent executing code in user mode (i.e., executing normal program code).
The time command displays the real time, the system CPU time, and user CPU time
taken to execute the processes in a pipeline.

The time command displays the real time, the system CPU time, and user CPU time
taken to execute the processes in a pipeline.

#### Client-Server Architecture

At various points in this book, we discuss the design and implementation of client-
server applications.

A client-server application is one that is broken into two component processes:

* a client, which asks the server to carry out some service by sending it a request
message; and

* a server, which examines the client’s request, performs appropriate actions, and
then sends a response message back to the client.

Sometimes, the client and server may engage in an extended dialogue of requests
and responses.

Servers may implement a variety of services, such as:

* providing access to a database or other shared information resource;
* providing access to a remote file across a network;
* encapsulating some business logic;
* providing access to a shared hardware resource (e.g., a printer); or
* serving web pages.

Encapsulating a service within a single server is useful for a number of reasons, such as the following:

* Efficiency: It may be cheaper to provide one instance of a resource (e.g., a printer) that is managed by a server than to provide the same resource locally on every computer.

* Control, coordination, and security: By holding a resource (especially an information resource) at a single location, the server can coordinate access to the resource (e.g., so that two clients don’t simultaneously update the same piece of information) or secure it so that it is made available to only selected clients.

* Operation in a heterogeneous environment: In a network, the various clients, and the server, can be running on different hardware and operating system platforms.

#### Realtime

Realtime applications are those that need to respond in a timely fashion to input. Frequently, such input comes from an external sensor or a specialized input device, and output takes the form of controlling some external hardware.

POSIX.1b defined a number of extensions to POSIX.1 for the support of real-time applications. These include asynchronous I/O, shared memory, memory-mapped files, memory locking, realtime clocks and timers, alternative scheduling policies, realtime signals, message queues, and semaphores. Even though they don’t strictly qualify as realtime, most UNIX implementations now support some or all of these extensions. (During the course of this book, we describe those features of POSIX.1b that are supported by Linux.)

The /proc File System

Like several other UNIX implementations, Linux provides a /proc file system, which consists of a set of directories and files mounted under the /proc directory. The /proc file system is a virtual file system that provides an interface to kernel data structures in a form that looks like files and directories on a file system. This provides an easy mechanism for viewing and changing various system attributes. In addition, a set of directories with names of the form /proc/ PID, where PID is a process ID, allows us to view information about each process running on the system.