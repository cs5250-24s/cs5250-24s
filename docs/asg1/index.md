# Introduction

!!! info

      Please read the [general instructions](../index.md) first.

      The assignment is due **17:59 on 12 Feb 2024**.
      Late submissions will lose **1 mark per hour**.

!!! info

      These assignments are designed under the assumption that you are using an x86-64 machine.
      If you do not have access to a machine based on the x86 architecture, you have the option to request a server for your use.
      To proceed with a server request, please complete and submit [this quiz](https://canvas.nus.edu.sg/courses/53045/quizzes/33666).
      However, in order to ensure the efficient use of resources, we kindly ask that you request a server only if there are no other viable alternatives.

## Part A (35 marks)

### [Task 1: Setup a Virtual Machine](task-vm.md)

**Question 1 (3 marks):**
What is the command needed inside the VM to make your shared folder accessible?
Please explain the arguments of the command.

### [Task 2: Build and Install Linux Kernel](task-kbuild.md)

**Question 2 (10 marks):**
Please submit the DEB package `linux-image-6.7.0..._amd64.deb` containing your customized smaller kernel.

### [Task 3: Explore the Boot Process](task-boot.md)

**Question 3 (5 marks):**
Please submit the BIOS image `bios.bin` which prints out your Matric No.

**Question 4 (5 marks):**
You'll notice that most of the executable files under the `_install/bin` directory are merely links to the `busybox` executable.
Interestingly, despite being links, each of these files can be executed and behaves differently.

Try to understand how this functionality is achieved and explain the underlying concept with a brief code snippet.

To demonstrate your understanding, you are required to write a C program named `dispatch.c`.
This program should be compiled into an executable named `dispatch`.
When a link (with different names) to `dispatch` is executed, it should output different messages based on the filename of the link.

Here are the expected outputs for different filenames:

| Filename | Expected Output                                                                    |
| -------- | ---------------------------------------------------------------------------------- |
| `hello`  | Prints `Hello World!`                                                              |
| `user`   | Prints the current username                                                        |
| `kernel` | Prints the kernel release version of the current system (e.g., `6.7.0-14-generic`) |

Here's an example of how the program should behave:

[![asciicast](https://asciinema.org/a/b3OtOhNR595XeR1FYPIMP35O5.svg)](https://asciinema.org/a/b3OtOhNR595XeR1FYPIMP35O5)

**Question 5 (8 marks):**
Please submit the initramfs image `initramfs.cpio.gz` which can switch to the root filesystem.

**Question 6 (4 marks):**
Where is the command line argument `systemd.unit=rescue.target` accepted in the boot process?
Is it processed by the BIOS, Kernel, Initramfs, or after switching to the root filesystem?
Please explain your answer.

### Submission Guidelines

For this assignment, you are required to answer Questions 1 and 6 in separate Markdown files.
If you're unfamiliar with Markdown syntax, you can find numerous online tutorials, such as the one available at [CommonMark](https://commonmark.org/help/tutorial/).

Your submission should be a zip file containing the following files:

| Filename                         | Description                                                                                         |
| -------------------------------- | --------------------------------------------------------------------------------------------------- |
| `qn1.md`                         | Your response to Question 1, formatted in Markdown.                                                 |
| `linux-image-6.7.0..._amd64.deb` | A DEB package that includes your custom, reduced-size kernel.                                       |
| `bios.bin`                       | A BIOS image that displays your Matriculation Number.                                               |
| `dispatch.c`                     | A C program that behaves differently depending on the name of the executable.                       |
| `initramfs.cpio.gz`              | An initramfs image that displays your Matriculation Number before switching to the root filesystem. |
| `qn6.md`                         | Your response to Question 6, formatted in Markdown.                                                 |

You can verify the contents of your zip file using the `unzip -l` command.
Please note that the files listed above are for illustrative purposes only.
The actual files in your submission should not be empty.

```console
$ zip asg1.zip bios.bin dispatch.c initramfs.cpio.gz linux-image-6.7.0*_amd64.deb qn1.md qn6.md
  adding: bios.bin (stored 0%)
  adding: dispatch.c (stored 0%)
  adding: initramfs.cpio.gz (stored 0%)
  adding: linux-image-6.7.0_6.7.0-3_amd64.deb (stored 0%)
  adding: qn1.md (stored 0%)
  adding: qn6.md (stored 0%)
$ unzip -l asg1.zip
Archive:  asg1.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  2024-01-15 19:17   bios.bin
        0  2024-01-15 19:17   dispatch.c
        0  2024-01-15 19:17   initramfs.cpio.gz
        0  2024-01-15 19:17   linux-image-6.7.0_6.7.0-3_amd64.deb
        0  2024-01-15 19:17   qn1.md
        0  2024-01-15 19:17   qn6.md
---------                     -------
        0                     6 files
```

!!! warning

    Your zip file shall have only one file ending with ".deb".
    If you have more than one file ending with ".deb", you will lose additional marks.

Please submit your zip file to [Assignment 1 (Part A)](https://canvas.nus.edu.sg/courses/53045/assignments/100154) on Canvas.

## Part B (15 marks)

Please complete the quiz [Assignment 1 (Part B)](https://canvas.nus.edu.sg/courses/53045/quizzes/34349) on Canvas.
