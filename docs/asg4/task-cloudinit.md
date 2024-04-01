# Task 0: Cloud-Init

In [Assignment 1](../asg1/task-boot.md), we delved into using QEMU to traverse
the boot procedure of a system, from BIOS to Ubuntu.
Additionally, [Assignment 2](../asg2/task-gdb.md) highlighted the potency of
QEMU in conjunction with GDB for understanding and debugging the kernel.

For this assignment, we'll use QEMU to emulate a non-existent peripheral device
and develop a device driver for it.
But before diving into that, let's establish a production-level virtual machine
using QEMU, transitioning away from a testing environment.

## Step 1: Boot using A Cloud Image

In [Assignment 1](../asg1/task-boot.md), we used a RootFS archive to create a
disk image, which did not involve any boot components.  For this task, we will
boot from a cloud image, which is a disk image containing a bootable operating
system.

Download the cloud image from the following link:
<https://cloud-images.ubuntu.com/releases/22.04/release/ubuntu-22.04-server-cloudimg-amd64.img>

We can directly boot from this image, but doing so would modify it. In a real
cloud environment, numerous virtual machines can be booted from the same image,
so we prefer not to alter the image itself. Instead, we create a new disk image
that is backed by the cloud image.

```bash
qemu-img create -f qcow2 -b ubuntu-22.04-server-cloudimg-amd64.img -F qcow2 hdd.qcow2 10G
```

Then, you can boot the image using the following command:

```bash
qemu-system-x86_64 -smp 2 -m 2G -nographic hdd.qcow2
```

Here, the `-smp` option specifies the number of CPUs, and the `-m` option
specifies the amount of memory. You can adjust these values based on your
system's resources.

## Step 2: Customize your VM using Cloud-Init

I assume you have been trying to log in to the VM but failed because you don't
know the password.  Similar to what we experienced in [Assignment
1](../asg1/task-boot.md), there isn't a user account created in the VM by
default. Hence, we need to create one before logging in.

Previously, we booted into the system in single-user mode to create a user, but
this approach isn't scalable. In a cloud environment, we can utilize a tool
called cloud-init to automate the process of creating a user account or other
configurations.
Let's follow the [tutorial](https://cloudinit.readthedocs.io/en/latest/tutorial/qemu.html).

First, create a directory to contain the files needed for cloud-init.

Then, within the directory, create a file named `user-data` with the following
content. Make sure to replace the username and password placeholders with your
own.

<script src="https://gist.github.com/shen-jiamin/b236b48ba982a9b2917171677e2f1073.js?file=user-data"></script>

Additionally, create two more files:

1. `meta-data` with the following content:

  ```yaml
  instance-id: someid/somehostname
  ```

2. `vendor-data`, which can be empty.

Finally, launch an HTTP server in the directory to host these files.

```bash
$ python3 -m http.server
```

Now you can boot the VM with cloud-init enabled.

```bash
qemu-system-x86_64 -smp 2 -m 2G -nographic -nic user \
  -smbios type=1,serial=ds='nocloud;s=http://10.0.2.2:8000/' \
  hdd.qcow2
```

In addition to the regular kernel outputs, you should see some cloud-init logs.
Eventually, you'll see something like "Reached target Cloud-init target." After
pressing "Enter," you should encounter the login prompt. You can now log in
using the username and password specified in `user-data`.

This is just a piece of cake for cloud-init.  You can accomplish even more with
it, such as setting up SSH keys, installing packages, and running scripts. For
further information, you can refer to the
[examples](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).


## Step 3: SSH into the VM

If you want to login to the VM using SSH, you can forward the SSH port from the
host to the VM.  You can do this by changing `-nic` option in the command

```bash
qemu-system-x86_64 -smp 2 -m 2G -nographic -nic user,hostfwd=tcp::2222-:22 hdd.qcow2
```

You can change the port number `2222` to any other port number you prefer.

## Step 4: Install a New Kernel

The kernel version in the cloud image is not v6.7, which is the version we will
use for the rest of the assignments. You can either build the kernel from source
and install it in the VM, or if you have a pre-built kernel on your host
machine, you can build a deb package and install it in the VM.
