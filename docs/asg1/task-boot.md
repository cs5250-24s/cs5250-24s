# Task 3: Experience the Boot Sequence

You should work in the VM you created in Task 1.

## Installing QEMU

QEMU is an open-source machine emulator that allows you to run virtual machines and emulate different architectures on your own system.
In this task, we will be leveraging the flexibility of QEMU, particularly its ability to specify a custom firmware or boot directly from a kernel image.

To install QEMU on your local machine, open your terminal and execute the following command:

```
sudo apt install qemu-system-x86
```

After installing QEMU, you can start using it by running the following command:

```
qemu-system-x86_64 -nographic
```

Upon execution, you'll see some output starting with `SeaBIOS (version 1.xxx)`, followed by some boot attempts.
Since we are not providing any bootable image at this point, it's normal for the booting process to fail or hang.

You can quit QEMU by pressing `Ctrl-a` then `x`.
For more commands in this mode, please refer to the [manual](https://www.qemu.org/docs/master/system/mux-chardev.html).

## Building SeaBIOS

SeaBIOS is an open-source implementation of a 16-bit x86 BIOS.

First, clone the SeaBIOS repository to get a copy of the source code.
You can do this by executing the following command in your terminal:

```bash
git clone https://git.seabios.org/seabios.git
```

Navigate to the cloned repository and build SeaBIOS using the `make` command.

```bash
cd seabios && make
```

This will generate a file named `bios.bin` in the `out` directory.
You can boot into SeaBIOS using the `bios.bin` file you just built. Run the following command:

```bash
qemu-system-x86_64 -nographic -bios out/bios.bin
```

Upon successful execution, the output should start with `SeaBIOS (version rel-1.16.3-xxxxx)`.
This version should be later than the one you saw previously, indicating that you have successfully booted into the newly built SeaBIOS.

!!! Question

    Please modify the source code so that your Matric No. (AxxxxxxxY) is printed out below the SeaBIOS version.
    Submit the `out/bios.bin` file.

## Building Linux Kernel

You should have already built the kernel in the previous task.

## Building an Initramfs

### Create a Simple Initramfs

Write a C program named `init.c` that prints out a message of your choice.
Be creative with your message.

```c
// Example of init.c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

Compile your program statically using the command below.
This will create an executable named `init`.

```
gcc -static -o init init.c
```

Create a new directory and move the `init` executable into it.
Ensure that the `init` executable is the only file in this directory.
Generate an initramfs from the directory containing your `init` executable using the following command:

```
find . | cpio -H newc -o | gzip > initramfs.cpio.gz
```

Boot into the kernel using your newly created initramfs by executing the following command:

```
qemu-system-x86_64 -nographic -append "console=ttyS0" -kernel /path/to/bzImage -initrd /path/to/initramfs.cpio.gz
```

Replace `/path/to/bzImage` and `/path/to/initramfs.cpio.gz` with the actual paths to your kernel image and initramfs respectively.

You will encounter a kernel panic.
But don't panic!
Look for the lines `Run /init as init process` and `Kernel panic - not syncing: Attempted to kill init!`.
Your message should appear between these two lines.
If you see your message, you've successfully built and booted an initramfs.

### Create an Initramfs using BusyBox

Start by downloading the latest stable version of the BusyBox source code from the official website: [BusyBox](https://busybox.net/).
After downloading, extract the source code and navigate to the directory containing the extracted source code.

Next, create a configuration using the `make menuconfig` command.
During this process, ensure that you select the "Build static binary (no shared libs)" option under "Settings".
Then, build the source code using `make` command.

After building the source code, install BusyBox using the `make install` command.
This will install BusyBox to the `_install` directory.

Next, create an `init` script in the `_install` directory.
This script will directly drop to a shell.
Here's a sample script, remember to replace `<your name>` and `<your Matric No.>` with your actual name and Matric No.:

```bash
#!/bin/sh

set -ex
echo "Hello, <your name> (<your Matric No.>)"
exec /bin/sh
```

The `set -ex` command facilitates debugging by printing each command before executing it and exiting immediately if any command fails.
Remember to make the script executable using the following command:

```
chmod +x init
```

Finally, pack all the files in the `_install` directory into an initramfs using the command used previously.
You may use QEMU to test if it works.

## Booting into Ubuntu

### Create a Hard Disk Image for the Ubuntu Root Filesystem

Download the Ubuntu 22.04 minimal cloud image from the following link:
[Ubuntu 22.04 minimal cloud image](https://cloud-images.ubuntu.com/minimal/releases/jammy/release/ubuntu-22.04-minimal-cloudimg-amd64-root.tar.xz)

We will be creating a hard disk image using the downloaded files.
First, create a file that is large enough to hold the root filesystem.
In this example, we will create a 16 GiB file and format it as an ext4 filesystem using the following commands:

```
fallocate -l 16GiB rootfs.img
mkfs.ext4 rootfs.img
```

Next, mount the image at a temporary location, extract the root filesystem into it, and then unmount it.
You can do this using the following commands:

```
mkdir rootfs
sudo mount -o loop ./rootfs.img ./rootfs
sudo tar xf ubuntu-22.04-minimal-cloudimg-amd64-root.tar.xz -C rootfs
sudo umount rootfs
```

At this point, the `rootfs.img` file is a hard disk image that is ready to be used as a root filesystem.

### Switch to rootfs from initramfs

BusyBox provides a `switch_root` command that can be used to switch to a new root filesystem.
You will need to find out how to use this command and modify the `init` script accordingly to switch to the root filesystem.

You can then boot into the root filesystem using the following command:

```
qemu-system-x86_64 -nographic -kernel bzImage -initrd busybox.cpio.gz -append 'console=ttyS0' rootfs.img
```

### Create a User

The root filesystem that we created does not have any users, which means you cannot log in to it.
To create a user, we will boot into the system in single-user mode (rescue mode) and use the `useradd` command.
To boot into rescue mode, add `systemd.unit=rescue.target` to the kernel command line using the following command:

```
qemu-system-x86_64 -nographic -kernel bzImage -initrd busybox.cpio.gz -append 'console=ttyS0 systemd.unit=rescue.target' rootfs.img
```

Once in rescue mode, you can create a user using the `useradd` command.
Please search online for instructions on how to use this command to create a user.
