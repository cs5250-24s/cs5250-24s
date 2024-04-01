# Task 1: Character Device Driver

## Overview

In Linux, hardware devices are presented to user space as device files, which
can be accessed using standard file I/O operations. These device files reside in
the "/dev" directory and are backed by device drivers, kernel components that
interact with the hardware device. Typically, device drivers are implemented as
modules. System calls like open, read, write, close, lseek, mmap, etc., made to
these device files are routed to the corresponding device driver for handling.

There exist two primary types of device files: character device files (chrdev)
and block device files (blkdev). These types are differentiated by the speed,
volume, and method of data transfer between the device and the system.

A character device file represents a slower device that transfers data in
streams of bytes, examples include keyboards, mice, serial ports, or terminals.
Managing these devices is relatively simple since they don't demand extremely
high performance.

In contrast, a block device file represents a faster device that transfers data
in fixed-size blocks, such as hard disks or flash drives. Block devices
typically offer higher speeds compared to character devices, and their
performance is critical. So Linux provides a different set of APIs for
interacting with block devices, allowing for common support features like
caching, buffering, and I/O scheduling to be implemented.

To identify device drivers in your kernel, use the command `ls -l /dev` and
examine the information in each column. The first letter in each line indicates
if it's a char device (represented as "c") or a block device (represented as
"b"). The major and minor numbers are also displayed. The major number
identifies the specific driver associated with the device. It is used by the
kernel to route system calls for the device to the appropriate driver. The
minor number is used by the kernel to determine the exact device being referred
to.
For example, in a disk driver, the minor number might specify a partition on the
disk.

```console
$ ls -l /dev/sd*
brw-rw---- 1 root disk 8, 0 Feb 20 16:36 /dev/sda
brw-rw---- 1 root disk 8, 1 Feb 20 16:36 /dev/sda1
brw-rw---- 1 root disk 8, 2 Feb 20 16:36 /dev/sda2
```

## Onebyte Character Device

In this task, we will see a character device driver that exposes the "onebyte"
hardware device to user space as a character device file.
"onebyte" can store only one byte, backed by a single byte of memory.

- When the driver is loaded, "onebyte" is initialized to -1.
- When the device is read, the byte is returned to the user.
- When the device is written, the byte is overwritten with the new value.

It is both "global" and "persistent", meaning that multiple file descriptors can
access the data inside, and its data remains available and unchanged even when
the device is closed and reopened.
By exposing it as a character device file, it can be accessed using standard
file I/O operations and tested with shell commands such as cat, echo, and I/O
redirection.

Before using the character device, it needs to be registered.
The `file_operations` structure needs to be set up, within which, we can define
the behaviour of the device by implementing the `open`, `release`, `read` and
`write` functions.

Please refer to the following code snippet for the implementation of the onebyte
character device driver.

<script src="https://gist.github.com/shen-jiamin/b236b48ba982a9b2917171677e2f1073.js?file=onebyte.c"></script>

!!! question

    Please explain, in detail, how the `container_of` macro works in the
    `onebyte_open` function, and how it's used to retrieve the `onebyte_data`
    structure.

    Additionally, Provide an explanation as to why the `onebyte_data` structure
    is stored as the private data of the file instead of being stored as a
    global variable?

Compile the code and load the module. You may find that the entry in the "/dev"
directory is not automatically created when the module is loaded.
These device files need to be created either:

- manually using the `mknod` command, which creates a special file that
  represents the device file, or
- automatically by the "udev" daemon.

For this task, we create the device file manually using the `mknod` command.

!!! question

    Provide the exact `mknod` command you used to create the device file.

Here's the expected behaviour of the device:

```console
$ sudo insmod onebyte.ko
$ xxd /dev/onebyte
00000000: ff                                       .
$ echo 'a' > /dev/onebyte
bash: echo: write error: File too large
$ xxd /dev/onebyte
00000000: 61                                       a
$ echo -n 'b' > /dev/onebyte
$ xxd /dev/onebyte
00000000: 62                                       b
$ sudo rmmod onebyte
$ sudo insmod onebyte.ko
$ xxd /dev/onebyte
00000000: ff                                       .
```
