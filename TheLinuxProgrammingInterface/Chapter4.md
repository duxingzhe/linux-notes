## FILE I/O : THE UNIVERSAL I/O MODEL

### Overview

All system calls for performing I/O refer to open files using a file descriptor, a (usually small) nonnegative integer. File descriptors are used to refer to all types of open files, including pipes, FIFOs, sockets, terminals, devices, and regular files. Each process has its own set of file descriptors.

The following are the four key system calls for performing file I/O (programming languages and software packages typically employ these calls only indirectly, via I/O libraries):

* fd = open(pathname, flags, mode) opens the file identified by pathname, returning a file descriptor used to refer to the open file in subsequent calls. If the file doesn’t exist, open() may create it, depending on the settings of the flags bitmask argument. The flags argument also specifies whether the file is to be opened for reading, writing, or both. The mode argument specifies the permissions to be placed on the file if it is created by this call. If the open() call is not being used to create a file, this argument is ignored and can be omitted.
* numread = read(fd, buffer, count) reads at most count bytes from the open file referred to by fd and stores them in buffer. The read() call returns the number of bytes actually read. If no further bytes could be read (i.e., end-of-file was encountered), read() returns 0.
* numwritten = write(fd, buffer, count) writes up to count bytes from buffer to the open file referred to by fd. The write() call returns the number of bytes actually written, which may be less than count.
* status = close(fd) is called after all I/O has been completed, in order to release the file descriptor fd and its associated kernel resources.

```
#include <sys/stat.h>
#include <fcntl.h>
#include "tlpi_hdr.h"

#ifndef BUF_SIZE
#define BUF_SIZE 1024
#endif

int main(int argc, char *argv[])
{
    int inputFd, outputFd, openFlags;
    mode_t filePerms;
    ssize_t numRead;
    char buf[BUF_SIZE];

    if(argc!=3||strcmp(argv[1], "--help")==0)
    {
        usageErr("%s old-file new-file\n", argv[0]);
    }

    inputFd=open(argv[1], O_RDONLY);
    if(inputFd==-1)
    {
        errExit("opening file %s", argv[1]);
    }

    openFlags=O_CREAT|O_WRONLY|O_TRUNC;
    filePerms=S_IRUSR|S_IWUSR|S_IRGRP|S_IWGRP|
                S_IROTH|S_IWOTH;
    outputFd=open(argv[2], openFlags, filePerms);
    if(outputFd==-1)
    {
        errExit("opening file %s", argv[2]);
    }

    while((numRead=read(inputFd, buf, BUF_SIZE))>0)
    {
        if(write(outputFd, buf, numread)!=numRead)
        {
            fatal("counldn't write hole buffer");
        }
    }

    if(numRead==-1)
    {
        errExit("read");
    }

    if(closse(inputFd)==-1)
    {
        errExit("close input");
    }

    if(close(outputFd)==-1)
    {
        errExit("close output");
    }

    exit(EXIT_SUCCESS);
}
```

Using I/O system calls

### Universality of I/O

One of the distinguishing features of the UNIX I/O model is the concept of universality of I/O. This means that the same four system calls—open(), read(), write(), and close()—are used to perform I/O on all types of files, including devices such as terminals. Consequently, if we write a program using only these system calls, that program will work on any type of file.

### Opening a File: open()

The open() system call either opens an existing file or creates and opens a new file.

```
#include <sys/stat.h>
#include <fcntl.h>

int open(const char *pathname, int flags, ... /* mode_t mode */);
```

The flags argument is a bit mask that specifies the access mode for the file, using one of the constants shown in Table 4-2.

Early UNIX implementations used the numbers 0, 1, and 2 instead of the names shown in Table 4-2. Most modern UNIX implementations define these constants to have those values. Thus, we can see that O_RDWR is not equivalent to O_RDONLY | O_WRONLY; the latter combination is a logical error.

```
fd=open("startup", O_RDONLY);
if(fd==-1)
{
    errExit("open");
}

fd=open("myfile", O_RDWR|O_CREAT|O_TRUNC, S_IRUSR|S_IWUSR);
if(fd==-1)
{
    errExit("open");
}

fd=open("w.log", O_RDWR|O_CREAT|O_TRUNC|O_APPEND, S_IRUSR|S_IWUSR)
if(fd==-1)
{
    errExit("open");
}
```

Examples of the use of open()

#### File descriptor number returned by open()

SUSv3 specifies that if open() succeeds, it is guaranteed to use the lowest-numbered unused file descriptor for the process. We can use this feature to ensure that a file is opened using a particular file descriptor. For example, the following sequence ensures that a file is opened using standard input (file descriptor 0).

#### The open() flags Argument

In some of the example open() calls shown in Listing 4-2, we included other bits (O_CREAT, O_TRUNC, and O_APPEND) in flags in addition to the file access mode. We now consider the flags argument in more detail.

The constants in Table 4-3 are divided into the following groups:

* File access mode flags: These are the O_RDONLY, O_WRONLY, and O_RDWR flags described earlier. They can be retrieved using the fcntl() F_GETFL operation (Section 5.3).
* File creation flags: These are the flags shown in the second part of Table 4-3. They control various aspects of the behavior of the open() call, as well as options for subsequent I/O operations. These flags can’t be retrieved or changed.
* Open file status flags: These are the remaining flags in Table 4-3. They can be retrieved and modified using the fcntl() F_GETFL and F_SETFL operations (Section 5.3). These flags are sometimes simply called the file status flags.

#### Errors from open()

If an error occurs while trying to open the file, open() returns –1, and errno identifies the cause of the error. The following are some possible errors that can occur (in addition to those already noted when describing the flags argument above):

EACCES

The file permissions don’t allow the calling process to open the file in the mode specified by flags. Alternatively, because of directory permissions, the file could not be accessed, or the file did not exist and could not be created.

EISDIR

The specified file is a directory, and the caller attempted to open it for writing. This isn’t allowed. (On the other hand, there are occasions when it can be useful to open a directory for reading. We consider an example in Section 18.11.)

EMFILE

The process resource limit on the number of open file descriptors has been reached (RLIMIT_NOFILE, described in Section 36.3).

ENFILE

The system-wide limit on the number of open files has been reached.

ENOENT

The specified file doesn’t exist, and O_CREAT was not specified, or O_CREAT as specified, and one of the directories in pathname doesn’t exist or is a symbolic link pointing to a nonexistent pathname (a dangling link).

EROFS

The specified file is on a read-only file system and the caller tried to open it for writing.

ETXTBSY

The specified file is an executable file (a program) that is currently executing. It is not permitted to modify (i.e., open for writing) the executable file associated with a running program. (We must first terminate the program in order to be able to modify the executable file.)

### The creat() System Call

In early UNIX implementations, open() had only two arguments and could not be used to create a new file. Instead, the creat() system call was used to create and open a new file.

```
#include <fcntl.h>
int creat(const char *pathname, mode_t mode);
```

The creat() system call creates and opens a new file with the given pathname, or if the file already exists, opens the file and truncates it to zero length. As its function result, creat() returns a file descriptor that can be used in subsequent system calls.

Calling creat() is equivalent to the following open() call:

```
fd = open(pathname, O_WRONLY | O_CREAT | O_TRUNC, mode);
```

Because the open() flags argument provides greater control over how the file is opened (e.g., we can specify O_RDWR instead of O_WRONLY), creat() is now obsolete, although it may still be seen in older programs.

### Reading from a File: read()

The read() system call reads data from the open file referred to by the descriptor fd.

```
#include <unistd.h>
ssize_t read(int fd, void *buffer, size_t count);
```

### Writing to a File: write()

The write() system call writes data to an open file.

```
#include <unistd.h>
ssize_t write(int fd, void *buffer, size_t count);
```

### Closing a File: close()

The close() system call closes an open file descriptor, freeing it for subsequent reuse by the process. When a process terminates, all of its open file descriptors are automatically closed.

```
#include <unistd.h>
int close(int fd);
```

### Changing the File Offset: lseek()

For each open file, the kernel records a file offset, sometimes also called the readwrite offset or pointer. This is the location in the file at which the next read() or write() will commence. The file offset is expressed as an ordinal byte position relative to the start of the file. The first byte of the file is at offset 0.

```
#include <unistd.h>
off_t lseek(int fd, off_t offset, int whence);
```
