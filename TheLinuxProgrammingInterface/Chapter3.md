### SYSTEM PROGRAMMING CONCEPTS

This chapter covers various topics that are prerequisites for system programming. We begin by introducing system calls and detailing the steps that occur during their execution. We then consider library functions and how they differ from system calls, and couple this with a description of the (GNU) C library.

#### System Calls

A system call is a controlled entry point into the kernel, allowing a process to request that the kernel perform some action on the process’s behalf. The kernel makes a range of services accessible to programs via the system call application programming interface (API). These services include, for example, creating a new process, performing I/O, and creating a pipe for interprocess communication. (The syscalls(2) manual page lists the Linux system calls.)

Before going into the details of how a system call works, we note some general points:

* A system call changes the processor state from user mode to kernel mode, so that the CPU can access protected kernel memory.

* The set of system calls is fixed. Each system call is identified by a unique number. (This numbering scheme is not normally visible to programs, which identify system calls by name.)

* Each system call may have a set of arguments that specify information to be transferred from user space (i.e., the process’s virtual address space) to kernel space and vice versa.

behind the scenes, many steps occur during the execution of
a system call. To illustrate this, we consider the steps in the order that they occur
on a specific hardware implementation, the x86-32. The steps are as follows:

1.The application program makes a system call by invoking a wrapper function in the C library.
2.The wrapper function must make all of the system call arguments available to the system call trap-handling routine (described shortly). These arguments are passed to the wrapper via the stack, but the kernel expects them in specific registers. The wrapper function copies the arguments to these registers.
3.Since all system calls enter the kernel in the same way, the kernel needs some method of identifying the system call. To permit this, the wrapper function
copies the system call number into a specific CPU register ( %eax ).
4.The wrapper function executes a trap machine instruction ( int 0x80 ), which causes the processor to switch from user mode to kernel mode and execute code pointed to by location 0x80 (128 decimal) of the system’s trap vector.
5.In response to the trap to location 0x80 , the kernel invokes its system_call() routine (located in the assembler file arch/i386/entry.S ) to handle the trap. This handler:

a) Saves register values onto the kernel stack (Section 6.5).

b) Checks the validity of the system call number.

c) Invokes the appropriate system call service routine, which is found by using the system call number to index a table of all system call service routines (the kernel variable sys_call_table). If the system call service routine has any arguments, it first checks their validity; for example, it checks that addresses point to valid locations in user memory. Then the service routine performs the required task, which may involve modifying values at addresses specified in the given arguments and transferring data between user memory and kernel memory (e.g., in I/O operations). Finally, the service routine returns a result status to the system_call() routine.

d) Restores register values from the kernel stack and places the system call return value on the stack.

e) Returns to the wrapper function, simultaneously returning the processor to user mode.

6.If the return value of the system call service routine indicated an error, the wrapper function sets the global variable errno (see Section 3.4) using this value. The wrapper function then returns to the caller, providing an integer return value indicating the success or failure of the system call.

#### Library Functions

A library function is simply one of the multitude of functions that constitutes the standard C library. (For brevity, when talking about a specific function in the rest of the book we’ll often just write function rather than library function.) The purposes of these functions are very diverse, including such tasks as opening a file, converting a time to a human-readable format, and comparing two character strings.

##### The Standard C Library; The GNU C Library (glibc)

There are different implementations of the standard C library on the various UNIX implementations. The most commonly used implementation on Linux is the GNU C library (glibc, http://www.gnu.org/software/libc/).

```
#include <gnu/libc-version.h>
const char *gnu_get_libc_version(void);
    Returns pointer to null-terminated, statically allocated string containing GNU C library version number
```

#### Handling Errors from System Calls and Library Functions

Almost every system call and library function returns some type of status value indicating whether the call succeeded or failed. This status value should always be checked to see whether the call succeeded. If it did not, then appropriate action should be taken—at the very least, the program should display an error message warning that something unexpected occurred.

##### Handling system call errors

The manual page for each system call documents the possible return values of the call, showing which value(s) indicate an error. Usually, an error is indicated by a return of –1.

```
fd = open(pathname, flags, mode);
if (fd == -1)
{
    /* Code to handle the error */
}
```

When a system call fails, it sets the global integer variable errno to a positive value that identifies the specific error. Including the &lt;errno.h> header file provides a declaration of errno, as well as a set of constants for the various error numbers. All of these symbolic names commence with E . The section headed ERRORS in each manual page provides a list of possible errno values that can be returned by each system call.

Here is a simple example of the use of errno to diagnose a system call error:

```
cnt = read(fd, buf, numbytes);
if (cnt == -1)
{
    if (errno == EINTR)
    fprintf(stderr, "read was interrupted by a signal\n");
    else
    {
        /* Some other error occurred */
    }
}
```

Successful system calls and library functions never reset errno to 0, so this variable may have a nonzero value as a consequence of an error from a previous call. Furthermore, SUSv3 permits a successful function call to set errno to a nonzero value (although few functions do this). Therefore, when checking for an error, we should always first check if the function return value indicates an error, and only then examine errno to determine the cause of the error.

The perror() function prints the string pointed to by its msg argument, followed by a message corresponding to the current value of errno.

```
#include <stdio.h>
void perror(const char * msg );
```

The strerror() function returns the error string corresponding to the error number given in its errnum argument.

```
#include <string.h>
char *strerror(int errnum );
    Returns pointer to error string corresponding to errnum
```

#### Handling errors from library functions

The various library functions return different data types and different values to indicate failure. (Check the manual page for each function.) For our purposes, library functions can be divided into the following categories:

* Some library functions return error information in exactly the same way as system calls: a –1 return value, with errno indicating the specific error. An example of such a function is remove(), which removes a file (using the unlink() systemcall) or a directory (using the rmdir() system call). Errors from these functions can be diagnosed in the same way as errors from system calls.

* Some library functions return a value other than –1 on error, but nevertheless set errno to indicate the specific error condition. For example, fopen() returns a NULL pointer on error, and the setting of errno depends on which underlying system call failed. The perror() and strerror() functions can be used to diagnose these errors.

* Other library functions don’t use errno at all. The method for determining the existence and cause of errors depends on the particular function and is documented in the function’s manual page. For these functions, it is a mistake to use errno, perror(), or strerror() to diagnose errors.

You can learn more in the book of _Advanced Programming in the Unix Environment_.

### Common Functions and Header Files

Most of the example programs include a header file containing commonly required definitions, and they also use a set of common functions. We discuss the header file and functions in this section.

#### Common header file

Listing 3-1 is the header file used by nearly every program in this book. This header file includes various other header files used by many of the example programs, defines a Boolean data type, and defines macros for calculating the minimum and maximum of two numeric values. Using this header file allows us to make the example programs a bit shorter.

The err_exit() function is similar to errExit(), but differs in two respects:

* It doesn’t flush standard output before printing the error message.
* It terminates the process by calling _exit() instead of exit(). This causes the process to terminate without flushing stdio buffers or invoking exit handlers.

#### Functions for parsing numeric command-line arguments

The header file in Listing 3-5 provides the declaration of two functions that we frequently use for parsing integer command-line arguments: getInt() and getLong(). The primary advantage of using these functions instead of atoi(), atol(), and strtol() is that they provide some basic validity checking of numeric arguments.

```
#include "tlpi_hdr.h"
int getInt(const char *arg, int flags, const char *name);
long getLong(const char *arg, int flags, const char *name);
```

The getInt() and getLong() functions convert the string pointed to by arg to an int or a long, respectively. If arg doesn’t contain a valid integer string (i.e., only digits and the characters + and -), then these functions print an error message and terminate the program.

#### Portability Issues

In this section, we consider the topic of writing portable system programs. We introduce feature test macros and the standard system data types defined by SUSv3, and then look at some other portability issues.

##### Feature Test Macros

Various standards govern the behavior of the system call and library function APIs (see Section 1.3). Some of these standards are defined by standards bodies such The Open Group (Single UNIX Specification), while others are defined by the two historically important UNIX implementations: BSD and System V Release 4 (and the associated System V Interface Definition).

One way that we can do this is by defining the macro in the program source code before including any header files:

```
#define _BSD_SOURCE 1
```

Alternatively, we can use the –D option to the C compiler:

```
$ cc -D_BSD_SOURCE prog.c
```

> The term feature test macro may seem confusing, but it makes sense if we look at things from the perspective of the implementation. The implementation decides which of the features available in each header it should make visible, by testing (with #if) which values the application has defined for these macros.

The following feature test macros are specified by the relevant standards, and consequently their usage is portable to all systems that support these standards:

_POSIX_SOURCE

If defined (with any value), expose definitions conforming to POSIX.1-1990 and ISO C (1990). This macro is superseded by _POSIX_C_SOURCE.

_POSIX_C_SOURCE

If defined with the value 1, this has the same effect as _POSIX_SOURCE. If defined with a value greater than or equal to 199309, also expose definitions for POSIX.1b (realtime).

_XOPEN_SOURCE

If defined (with any value), expose POSIX.1, POSIX.2, and X/Open (XPG4) definitions. If defined with the value 500 or greater, also expose SUSv2 (UNIX 98 and XPG5) extensions.
