## FILE I/O : THE UNIVERSAL I/O MODEL

### Overview

All system calls for performing I/O refer to open files using a file descriptor, a (usually small) nonnegative integer. File descriptors are used to refer to all types of open files, including pipes, FIFOs, sockets, terminals, devices, and regular files. Each process has its own set of file descriptors.

The following are the four key system calls for performing file I/O (programming languages and software packages typically employ these calls only indirectly, via I/O libraries):

* fd = open(pathname, flags, mode) opens the file identified by pathname, returning a file descriptor used to refer to the open file in subsequent calls. If the file doesn’t exist, open() may create it, depending on the settings of the flags bitmask argument. The flags argument also specifies whether the file is to be opened for reading, writing, or both. The mode argument specifies the permissions to be placed on the file if it is created by this call. If the open() call is not being used to create a file, this argument is ignored and can be omitted.
* numread = read(fd, buffer, count) reads at most count bytes from the open file referred to by fd and stores them in buffer. The read() call returns the number of bytes actually read. If no further bytes could be read (i.e., end-of-file was encountered), read() returns 0.
* numwritten = write(fd, buffer, count) writes up to count bytes from buffer to the open file referred to by fd. The write() call returns the number of bytes actually written, which may be less than count.
* status = close(fd) is called after all I/O has been completed, in order to release the file descriptor fd and its associated kernel resources.