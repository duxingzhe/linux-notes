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