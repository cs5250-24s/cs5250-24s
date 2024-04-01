# Task 2: PCI Device

A PCI (Peripheral Component Interconnect) device is a type of hardware device
that connects to a computer's motherboard using the PCI interface. PCI devices
can be internal or external and are used for a wide range of applications, such
as graphics and audio processing, networking, and storage. The PCI bus is a
high-speed data path that allows the CPU to communicate with the device and
provides a common interface for a variety of hardware devices. PCI devices are
identified by a unique vendor ID and device ID and require a specific driver to
communicate with the operating system. They can be added or removed while the
computer is running, making them a convenient and flexible option for expanding
a system's capabilities.

In this task, you will be emulating a PCI device using QEMU and interacting with
it. The first step in this process is to set up the QEMU environment.

### Step 1: Launch a VM on QEMU

Continuing from the steps outlined in [Task 0](task-cloudinit.md), add an
additional flag to the QEMU command to emulate the PCI device.

```
  -device edu
```

You can add more copies of the `-device edu` flag to create multiple instances
of the PCI device.

### Step 2: Explore the PCI device

Use the `lspci` command to discover the PCI slot name of the device with a
vendor ID of 0x1234 and a device ID of 0x11e8. Refer to the man page of the
`lspci` command for additional information if needed.

Linux provides a mechanism to access PCI device resources through the sysfs. You
can read the kernel documentation on [Accessing PCI device resources through
sysfs](https://www.kernel.org/doc/html/v6.7/PCI/sysfs-pci.html). As a next step,
you can use the `hexdump` command to read the content of the PCI device's
configuration space. Refer to the specification of the PCI configuration space,
available
[here](https://en.wikipedia.org/wiki/PCI_configuration_space),
to make sense of the output.

The file `resource0` in the directory is mapped to the device's memory-mapped
I/O (MMIO) area, and part of its specification is given below.

```
MMIO area spec
--------------

Only size == 4 accesses are allowed for addresses < 0x80. size == 4 or
size == 8 for the rest.

0x00 (RO) : identification (0xRRrr00edu)
            RR -- major version
            rr -- minor version

0x04 (RW) : card liveness check
            It is a simple value inversion (~ C operator).

0x08 (RW) : factorial computation
            The stored value is taken and factorial of it is put back here.
            This happens only after factorial bit in the status register (0x20
            below) is cleared.

0x20 (RW) : status register, bitwise OR
            0x01 -- computing factorial (RO)
            0x80 -- raise interrupt after finishing factorial computation
```

You may find the source code of the device
[here](https://github.com/qemu/qemu/blob/v6.2.0/hw/misc/edu.c) and a full copy
of its specification
[here](https://github.com/qemu/qemu/blob/v6.2.0/docs/specs/edu.txt).

However, when attempting to read the first four bytes of the file using the
`head -c 4 resource0` command, an "Input/output error" is encountered:

```bash
# head -c 4 resource0
head: error reading 'resource0': Input/output error
```

!!! question

    Why can't we read from the file `resource0`?

    Hint:

    - According to the
      [documentation](https://www.kernel.org/doc/html/v6.7/PCI/sysfs-pci.html),
      `rw` access is allowed for `IORESOURCE_IO` regions only in files named
      `resource0..N`.
    - You should refer to the information from the configuration space.

### Step 3: Access the PCI device

Since the file is mmapable, we can use the `mmap` system call to map the file
into the process's address space and then access the device's registers. For
more information about the `mmap` system call, you can refer to the [man
page](https://www.man7.org/linux/man-pages/man2/mmap.2.html).

Please write a program that accepts two arguments:

1. The path to the PCI device's "resource0".
2. A 32-bit unsigned integer in hexadecimal format.

The program should successfully output the identification of the device and the
inversion of the given number.

Here's the expected behavior of the program:

```console
$ sudo ./pci-mmap /sys/path/to/the/device/resource0 0x11111111
Identification: 0x010000edu
     Inversion: 0xeeeeeeee
```

!!! tip

    You should be able to accomplish the task by inserting some lines in the
    given slot. However, feel free to modify other parts of the code if needed.

<script src="https://gist.github.com/shen-jiamin/b236b48ba982a9b2917171677e2f1073.js?file=pci-mmap.c"></script>

!!! question

    Please finish the implementation under the directory `task2-mmap/` in the
    repository.
